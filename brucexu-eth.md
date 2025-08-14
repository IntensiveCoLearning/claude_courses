---
timezone: UTC+12
---

# Bruce Xu

**GitHub ID:** brucexu-eth

**Telegram:** @brucexu_eth

## Self-introduction

在看 AI 的 E 卫兵

## Notes

<!-- Content_START -->
# 2025-08-14

# Features of Claude

## Image

Claude's vision capabilities let you include images in your messages and ask Claude to analyze them in countless ways. You can ask Claude to describe what's in an image, compare multiple images, count objects, or perform complex visual analysis tasks.

There are several important limitations to keep in mind when working with images:

- Up to 100 images across all messages in a single request
- Max size of 5MB per image
- When sending one image: max height/width of 8000px
- When sending multiple images: max height/width of 2000px
- Images can be included as base64 encoding or a URL to the image
- Each image counts as tokens based on its dimensions: tokens = (width px × height px) / 750

# 2025-08-13

# Features of Claude

## Extended Thinking

When extended thinking is enabled, Claude's response changes from a simple text block to a structured response containing two parts:

- Thinking Block
- Text Block

With thinking enabled, you get both the reasoning process and the final answer:

- Signature: A cryptographic token that ensures you haven't modified the thinking text.

When to Use Extended Thinking
The decision is straightforward: use your prompt evaluations. Run your prompts without thinking first, and if the accuracy isn't meeting your requirements after you've already optimized your prompt, then consider enabling extended thinking.

# 2025-08-12

# RAG

How Re-ranking Works

Re-ranking is conceptually simple. After running your vector index and BM25 index and merging the results, you add one more step: a re-ranker that uses Claude to intelligently reorder your search results.

The Re-ranking Prompt

```
You are tasked with finding the documents most relevant to a user's question.

<user_question>
What happened with INC-2023-Q4-011?
</user_question>

Here are documents that may be relevant:
<documents>
<document>Section 10...</document>
<document>Section 2...</document>
<document>Section 7...</document>
<document>Section 6...</document>
</documents>

Return the 3 most relevant docs, in order of decreasing relevance.
```

Efficiency Considerations

A better approach is to assign each text chunk a unique ID ahead of time, then ask Claude to return just those IDs in the correct order:

```
<documents>
<document>
<id>ab84</id>
<content>Section 10...</content>
</document>
<document>
<id>51n3</id>
<content>Section 8...</content>
</document>
</documents>
```
Claude can then return a simple list like ["1p5g", "51n3", "ab83"] instead of copying entire text chunks.

Werid, my output order is incorrect.

## Contextual retrieval

Contextual retrieval is a technique that improves RAG pipeline accuracy by solving a fundamental problem: when you split a document into chunks, each chunk loses its connection to the broader document context.

Contextual retrieval adds a preprocessing step before inserting chunks into your retriever database. Here's the process:

- Take each individual chunk and the original source document
- Send both to Claude with a specific prompt
- Ask Claude to write a short snippet that situates the chunk within the overall document
- Combine this context with the original chunk to create a "contextualized chunk"
- Use the contextualized chunk in your vector and BM25 indexes

Handling Large Documents

you can provide a reduced set of context instead of the entire document. The strategy is to include:

- A few chunks from the start of the document (often containing summaries or abstracts)
- Chunks immediately before the chunk you're contextualizing
- Skip chunks in the middle that are less relevant to the current chunk

This approach gives Claude enough context to understand what the document is about and how the current chunk fits in, without overwhelming the prompt with unnecessary text.

# 2025-08-11

# RAG

## Step 1: Chunking the Text

```
with open("./report.md", "r") as f:
    text = f.read()

chunks = chunk_by_section(text)
chunks[2]  # Test to see the table of contents
```

## Step 2: Generate Embeddings

```
embeddings = generate_embedding(chunks)
```

## Step 3: Store in Vector Database

```
store = VectorIndex()

for embedding, chunk in zip(embeddings, chunks):
    store.add_vector(embedding, {"content": chunk})
```

Notice that we store both the embedding and the original text content. This is crucial because when we search later, we need to return the actual text, not just the numerical embedding values.

The embedding numbers are just indexes for the chunk.

## Step 4: Process User Queries

```
user_embedding = generate_embedding("What did the software engineering dept do last year?")
```

## Step 5: Find Relevant Content

```
results = store.search(user_embedding, 2)

for doc, distance in results:
    print(distance, "\n", doc["content"][0:200], "\n")
```

## bm25

When building RAG pipelines, you'll quickly discover that semantic search alone doesn't always return the best results. Sometimes you need exact term matches that semantic search might miss. The solution is to combine semantic search with lexical search using a technique called BM25.

While semantic search excels at understanding context and meaning, it might return sections that are semantically related but don't actually contain the exact term you're looking for.

The solution is to run both semantic and lexical searches in parallel, then merge the results.

- Semantic search finds conceptually related content using embeddings
- Lexical search finds exact term matches using classic text search
- Merged results combine both approaches for better accuracy

BM25 (Best Match 25) is a popular algorithm for lexical search in RAG systems. Here's how it processes a search query:

Step 1: Tokenize the query
Break the user's question into individual terms. For example, "a INC-2023-Q4-011" becomes ["a", "INC-2023-Q4-011"].

Step 2: Count term frequency
See how often each term appears across all your documents. Common words like "a" might appear 5 times, while specific terms like "INC-2023-Q4-011" might appear only once.

Step 3: Weight terms by importance
Terms that appear less frequently get higher importance scores. The word "a" gets low importance because it's common, while "INC-2023-Q4-011" gets high importance because it's rare.

Step 4: Find best matches
Return documents that contain more instances of the higher-weighted terms.

The BM25 algorithm prioritizes sections that actually contain your specific search terms, especially rare terms like incident IDs.

BM25 excels at finding exact matches because it:

- Gives higher weight to rare, specific terms
- Ignores common words that don't add search value
- Focuses on term frequency rather than semantic meaning
- Works especially well for technical terms, IDs, and specific phrases

## Retriever

Merging results from different search methods isn't as simple as just concatenating lists. Each method uses different scoring systems, so we need a way to normalize and combine their rankings fairly.

Let's say we search for information about "INC-2023-Q4-011" and get these results:

- VectorIndex returns: Section 2 (rank 1), Section 7 (rank 2), Section 6 (rank 3)
- BM25Index returns: Section 6 (rank 1), Section 2 (rank 2), Section 7 (rank 3)

We combine these into a single table showing each text chunk's rank from both indexes, then apply the RRF formula:

```
RRF_score(d) = Σ(1 / (k + rank_i(d)))
```

For our example:

- Section 2: 1.0/(1+1) + 1.0/(1+2) = 0.833 Final rank: 1
- Section 7: 1.0/(1+2) + 1.0/(1+3) = 0.583 2
- Section 6: 1.0/(1+3) + 1.0/(1+1) = 0.75 3

# 2025-08-09

# RAG

After breaking a document into chunks, the next step in a RAG pipeline is finding which chunks are most relevant to a user's question. This is essentially a search problem - you need to look through all your text chunks and identify the ones that relate to what the user is asking about.

## Semantic Search

The most common approach for finding relevant chunks is semantic search. semantic search uses text embeddings to understand the meaning and context of both the user's question and each text chunk. 

## Text Embeddings

A text embedding is a numerical representation of the meaning contained in some text. 

- You feed text into an embedding model
- The model outputs a long list of numbers (the embedding)
- Each number ranges from -1 to +1
- These numbers represent different qualities or features of the input text

Each number in an embedding is essentially a "score" for some quality of the input text. However, here's the important caveat: we don't know precisely what each number represents.

So it looks like magic, and it is generated by embedding model. While it's helpful to imagine that one number might represent "how happy the text is" or "how much the text talks about oceans," these are just conceptual examples.

## RAG pipeline step by step

### Step 1: Chunk Your Source Text

break it into manageable chunks

### Step 2: Generate Embeddings

we convert each text chunk into numerical embeddings using an embedding model.

The embedding API typically performs a normalization step that scales each vector to have a magnitude of 1.0. This gives us normalized vectors like [0.944, 0.331] and [0.295, 0.955].

### Step 3: Store in Vector Database

We store these embeddings in a vector database - a specialized database optimized for storing, comparing, and searching through long lists of numbers like our embeddings.

Above are preprocessing works.

### Step 4: Process User Query

When a user asks a question like "I'm curious about the company. In particular, what did the software engineering dept do this year?", we run their query through the same embedding model.

So with the same embedding model, we can get similarity to match (I guess?).

### Step 5: Find Similar Embeddings

We send the user's query embedding to our vector database and ask it to find the most similar stored embeddings.

The vector database calculates Cosine Similarity to determine related items.

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748542288%2F07_-_004_-_The_Full_RAG_Flow_15.1748542288516.jpg)

Key points about cosine similarity:

- Results range from -1 to 1
- Values close to 1 mean high similarity
- Values close to -1 mean very different
- 0 means perpendicular (no relationship)

Cosine Distance is simply calculated as (1 - cosine similarity): Values close to 0 mean high similarity and larger values mean less similarity.

### Step 6: Create the Final Prompt

Finally, we take the user's question and the most relevant text chunk we found, combine them into a prompt, and send it to Claude for a response.

# 2025-08-08

I asked a question regarding "word_count": How does word_count work? Claude: The key insight is that LLMs don't truly "predict" future token generation in a deterministic way - they generate probabilistically token by token. Any early word_count is either based on already-generated content or is an estimate that could be wrong.

# RAG

Retrieval Augmented Generation (RAG) is a technique that helps you work with large documents that are too big to fit into a single prompt. Instead of cramming everything into one massive prompt, RAG breaks documents into chunks and only includes the most relevant pieces when answering questions.

RAG takes a smarter approach. First, you break the document into smaller chunks during a preprocessing step. Then, when a user asks a question, you find the chunks most relevant to their question and only include those in your prompt.

Challenges with RAG

- Requires a preprocessing step to chunk documents
- Need a search mechanism to find "relevant" chunks
- Included chunks might not contain all the context Claude needs
- Many ways to chunk text - which approach is best?

For example, you could split documents into equal-sized portions, or you could create chunks based on document structure like headers and sections. Each approach has trade-offs you'll need to evaluate for your specific use case.

Text chunking is one of the most critical steps in building a RAG (Retrieval Augmented Generation) pipeline. How you break up your documents directly impacts the quality of your entire system. A poor chunking strategy can lead to irrelevant context being inserted into your prompts, causing your AI to give completely wrong answers.

### Size-Based Chunking

downsides:

- Words get cut off mid-sentence
- Chunks lose important context from surrounding text
- Section headers might be separated from their content

To address these issues, you can add overlap between chunks.

### Structure-Based Chunking

Structure-based chunking divides text based on the document's natural structure - headers, paragraphs, and sections. This works great when you have well-formatted documents like Markdown files.

### Semantic-Based Chunking

Semantic-based chunking is the most sophisticated approach. You divide text into sentences, then use natural language processing to determine how related consecutive sentences are. You build chunks from groups of related sentences.

### Sentence-Based Chunking

A practical middle ground is chunking by sentences. You split the text into individual sentences using regular expressions, then group them into chunks with optional overlap:

Remember: there's no single "best" chunking strategy. The right approach depends on your specific documents, use cases, and the trade-offs you're willing to make between implementation complexity and chunk quality.

# 2025-08-07

# Tool Use

With streaming enabled, for tool use, you'll also need to handle a new event type called InputJsonEvent.

Each InputJsonEvent contains two key properties:

- partial_json - A chunk of JSON representing part of the tool arguments
- snapshot - The cumulative JSON built up from all chunks received so far

Here's how you handle these events in your streaming pipeline:

```
for chunk in stream:
    if chunk.type == "input_json":
        # Process the partial JSON chunk
        print(chunk.partial_json)
        # Or use the complete snapshot so far
        current_args = chunk.snapshot
```

How JSON Validation Works

The Anthropic API doesn't immediately send you every chunk as Claude generates it. Instead, it buffers chunks and validates them first. This is enabled by default. The API will:

- Wait until the entire abstract value is complete
- Validate that key-value pair against your schema
- Send all the buffered chunks for abstract at once
- Repeat the process for the meta object

Fine-Grained Tool Calling

If you need faster, more granular streaming - perhaps to show users immediate updates or start processing partial results quickly - you can enable fine-grained tool calling.

Fine-grained tool calling does one main thing: it disables JSON validation on the API side. This means:

You get chunks as soon as Claude generates them
No buffering delays between top-level keys
More traditional streaming behavior
Critical: JSON validation is disabled - your code must handle invalid JSON
Enable it by adding fine_grained=True to your API call:

```
run_conversation(
    messages, 
    tools=[save_article_schema], 
    fine_grained=True
)
```

With fine-grained tool calling, you might receive a word_count value much earlier in the stream, without waiting for the entire meta object to be completed.

TODO interesting, how does word_count work? How AI predict how mange words while generating?

Without fine-grained tool calling, the API's validation would catch this error and potentially wrap problematic values in strings, which might not match your expected schema.

Consider enabling fine-grained tool calling when:

- You need to show users real-time progress on tool argument generation
- You want to start processing partial tool results as quickly as possible
- The buffering delays negatively impact your user experience
- You're comfortable implementing robust JSON error handling

For most applications, the default behavior with validation is perfectly adequate. But when you need that extra responsiveness, fine-grained tool calling gives you the control to get chunks as fast as Claude can generate them.

## The text edit tool

Important Note: Tool version strings can for all model versions can be found here: https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/text-editor-tool

Claude comes with one built-in tool that you don't need to create from scratch: the text editor tool. This tool gives Claude the ability to work with files and directories just like you would in a standard text editor.

Here's where things get a bit confusing: while the tool schema is built into Claude, you still need to provide the actual implementation. This is the right design, as we can custom the access control and beheavier of the file handling implementation.

## Web search tool

Important note: Your organization must enable the Web Search tool in the settings console before using it. You can find this setting here: https://console.anthropic.com/settings/privacy

Claude includes a built-in web search tool that lets it search the internet for current or specialized information to answer user questions. Unlike other tools where you need to provide the implementation, Claude handles the entire search process automatically - you just need to provide a simple schema to enable it.

To use the web search tool, you create a schema object with these required fields:

web_search_schema = {
    "type": "web_search_20250305",
    "name": "web_search", 
    "max_uses": 5
}

The max_uses field limits how many searches Claude can perform. Claude might do follow-up searches based on initial results, so this prevents excessive API calls. A single search returns multiple results, but Claude may decide additional searches are needed.

You can get the WebSearchToolResultBlock, etc. from the response. You can also restrict search domains etc.

# 2025-08-05

# Tool Use

When you need structured data from Claude, you have two main approaches: prompt-based techniques using message prefills and stop sequences, or a more robust method using tools. 

While the prompt-based approach is simpler to set up, tools provide more reliable output at the cost of additional complexity.

Instead of hoping Claude formats its response correctly, you're essentially giving Claude a function to call with specific parameters that match your desired output structure.

Here's how the process works:

- Write a schema that describes the structure of data you're looking for
- Force Claude to use a tool with the tool_choice parameter
- Extract the structured data from the tool use response
- No need to provide a follow-up response - you're done once you get the data

tool_choice

- {"type": "auto"} - Model decides if it needs to use a tool (default)
- {"type": "any"} - Model must use a tool, but can choose which one
- {"type": "tool", "name": "TOOL_NAME"} - Model must use the specified tool

# 2025-08-04

## Tool  Use

The Multi-Turn Tool Pattern

Here's what happens behind the scenes when Claude needs multiple tools:

- User asks: "What day is 103 days from today?"
- Claude responds with a tool use block requesting get_current_datetime
- Your server calls the function and returns the result
- Claude realizes it needs more information and requests add_duration_to_datetime
- Your server calls that function and returns the result
- Claude now has enough information to provide the final answer

Building a Conversation Loop

```
def run_conversation(messages):
    while True:
        response = chat(messages)
        
        add_user_message(messages, response)
        
        if response.stop_reason != "tool_use":
            break
            
        tool_result_blocks = run_tools(response)
        add_user_message(tool_result_blocks)
        
    return messages

```

Bascially, just a deadloop to query Claude multiple times (with full context) until they think no more tool needed and combine all information and give it back to you. I think there might be some cache logic in Claude for saving some resources?

TODO Since there are so many tools, we may need to train a lightweight routing model to find and compose the relevant ones, then pass the consolidated results to Claude.

### The batch tool

The above way creates unnecessary back-and-forth communication when the operations could be performed simultaneously.

A batch tool is an additional tool that accepts a list of calls to other tools. Instead of Claude calling multiple tools directly, it calls the batch tool and provides arguments describing which tools it wants to run.

Rather than batch run tools on the server, you will define a batch_tool schema and send it to Claude to give you a "batch tool" tool call, and then you run the tool logic. In this way, you don't need to concern about the dependencies between tool calls.

```
batch_tool_schema = {
    "name": "batch_tool",
    "description": "Invoke multiple other tool calls simultaneously",
    "input_schema": {
        "type": "object",
        "properties": {
            "invocations": {
                "type": "array",
                "description": "The tool calls to invoke",
                "items": {
                    "type": "object",
                    "properties": {
                        "name": {
                            "type": "string",
                            "description": "The name of the tool to invoke"
                        },
                        "arguments": {
                            "type": "object",
                            "description": "The arguments to pass to the tool"
                        }
                    }
                }
            }
        }
    }
}
```

```
def run_batch(invocations=[]):
    batch_output = []
    
    for invocation in invocations:
        name = invocation["name"]
        args = json.loads(invocation["arguments"])
        
        tool_output = run_tool(name, args)
        
        batch_output.append({
            "tool_name": name,
            "output": tool_output
        })
    
    return batch_output
```

Once implemented, Claude will be much more likely to use the batch tool when it recognizes that multiple operations can be performed in parallel.

# 2025-08-03

## Tool Use

After Claude requests a tool call, you need to execute the function and send the results back. 

After running the tool function, you need to send the results back to Claude using a tool result block.

The tool result block has several important properties:

- tool_use_id - Must match the id of the ToolUse block that this ToolResult corresponds to
- content - Output from running your tool, serialized as a string
- is_error - True if an error occurred

Each tool call gets a unique ID, and you must match these IDs when sending back results. This ensures Claude knows which result corresponds to which request, even if the results arrive in a different order.

TODO How do we handle tool-call dependencies, e.g., when call #2 consumes call #1's output?

```
messages.append({
    "role": "user",
    "content": [{
        "type": "tool_result",
        "tool_use_id": response.content[1].id,
        "content": "15:04:22",
        "is_error": False
    }]
})
```

The complete message history now contains:

- Original user message
- Assistant message with tool use block
- User message with tool result block

OMG, if the user messages is complex with multi tool calls, there will be a huge amount of tokens...

When sending the follow-up request, you must still include the tool schema even though you're not expecting Claude to make another tool call. Claude needs the schema to understand the tool references in your conversation history.

> Claude behaves like a stateless service, keep in mind when designing apps.

Claude will then respond with a final message that incorporates the tool results into a natural response for the user. The tool use workflow is now complete - you've successfully enabled Claude to access real-time information through your custom function.

# 2025-08-02

## Tool use

Tools allow Claude to access information from the outside world, extending its capabilities beyond what it learned during training

Tool use solves this limitation by creating a structured way for Claude to request and receive fresh information.

When a user asks about current weather, you include instructions in your prompt about how to retrieve weather data. Claude recognizes it needs current information and requests weather data for the specific location. Your server then calls a weather API to get real-time conditions and sends that data back to Claude. Finally, Claude combines the fresh weather data with the user's question to provide an accurate, current response.

## Best Practices for Tool Functions

- Use descriptive names: Both your function name and parameter names should clearly indicate their purpose
- Validate inputs: Check that required parameters aren't empty or invalid, and raise errors when they are
- Provide meaningful error messages: Claude can see error messages and might retry the function call with corrected parameters

## Process

- Tool functions: Basically functions like API calls
- Tool schemas: Register your Tools to Claude (TODO different from MCP?)
- Making Tool-Enabled API Calls: To enable Claude to use tools, you need to include a tools parameter in your API call.

## Tool schemas

The complete tool specification has three main parts:

- name - A clear, descriptive name for your tool (like "get_weather")
- description - What the tool does, when to use it, and what it returns
- input_schema - The actual JSON schema describing the function's arguments

You can use Claude to generate tool JSON schemas:

1. give your code
2. ask for generating JSON schema for tool calling
3. add Anthropic doc on tool use
4. generate by following best practices

## Making Tool-Enabled API Calls

```
messages = []
messages.append({
    "role": "user",
    "content": "What is the exact time, formatted as HH:MM:SS?"
})

response = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=messages,
    tools=[get_current_datetime_schema],
)
```

Put schema in the tools, and Claude with check wether it needs tool calls or not. Wehn Claude decides to use a tool, it returns an assistant message with multiple blocks in the content list. Multi-block messages include: Text Block and ToolUse Block.

The ToolUse block includes:

- An ID for tracking the tool call
- The name of the function to call (like "get_current_datetime")
- Input parameters formatted as a dictionary
- The type designation "tool_use"

The tool usage process:

- Send user message with tool schema to Claude
- Receive assistant message with text block and tool use block
- Extract tool information and execute the actual function
- Send tool result back to Claude along with complete conversation history
- Receive final response from Claude

# 2025-08-01

## Prompt Engineering

### Be ing specific 

you can provide clear guidelines or steps that direct Claude toward the kind of output you're looking for.

Two Types of Guidelines:

- List qualities that the output should have
- Provide steps the model should follow

Output Quality Guidelines:

- Length of the response
- Structure and format
- Specific attributes or elements to include
- Tone or style requirements

Process Steps:

- Brainstorm three talents that would create dramatic tension
- Pick the most interesting talent
- Outline a pivotal scene that reveals the talent
- Brainstorm supporting character types that could increase the impact

Example (for a meal planning prompt):

```
Guidelines:
1. Include accurate daily calorie amount
2. Show protein, fat, and carb amounts  
3. Specify when to eat each meal
4. Use only foods that fit restrictions
5. List all portion sizes in grams
6. Keep budget-friendly if mentioned
```

Good prompt needs experts' experience and domain knowledge, this is the key for building AI apps.

Always Use Output Guidelines and Use Process Steps For Complex Problems.

For instance, if you're asking Claude to analyze why a sales team's performance dropped, you'd want to guide it through examining market metrics, industry changes, individual performance, organizational changes, and customer feedback - rather than letting it focus on just one potential cause.

### Structure with XML tags

XML tags provide a simple way to add structure and clarity to your prompts, especially when you're interpolating large amounts of data.

```
Debug my code below ... using provided docs:

from datavortex import xxx

# docs...
```

Better one:

```
Debug my code below ... using provided docs:

<code>
from datavortex import xxx

</code>

<docs>
# docs...
</docs>
```

When to Use XML Tags

- Including large amounts of context or data
- Mixing different types of content (code, documentation, data)
- You want to be extra clear about content boundaries
- Working with complex prompts that interpolate multiple variables

Example:

```
<athlete_information>
- Height: 6'2"
- Weight: 180 lbs
- Goal: Build muscle
- Dietary restrictions: Vegetarian
</athlete_information>

Generate a meal plan based on the athlete information above.
```

### Providing examples

Providing examples in your prompts is one of the most effective prompt engineering techniques you'll use. This approach, known as "one-shot" or "multi-shot" prompting, involves giving Claude sample input/output pairs to guide its responses.

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748623646%2F05_-_005_-_Providing_Examples_04.1748623646206.png)

The improved prompt includes:

- A clear positive example: "Great game tonight!" → "Positive"
- A sarcastic example: "Oh yeah, I really needed a flight delay tonight! Excellent!" → "Negative"
- Context explaining why sarcasm should be treated carefully

Notice how the examples are wrapped in XML tags like <sample_input> and <ideal_output>. This structure makes it crystal clear to Claude what each part represents.

Finding Good Examples from Evaluations and use as examples.

Don't just provide the input/output pair - explain why the output is good will provide additional context to help Claude understand the reasoning.

# 2025-07-31

## Prompt Engineering

Prompt engineering is about taking a prompt you've written and improving it to get more reliable, higher-quality outputs. This process involves iterative refinement - starting with a basic prompt, evaluating its performance, then systematically applying engineering techniques to improve it.

- Set a goal
- Write an initial prompt
- Eval the prompt
- Apply a prompt engineering technique
- Re-eval to verify better performance
- and repeat the last two steps

It is good to build a report for test cases, scores, and results for prompts.

Normally, we should include the following in the report:

- Scenario: background and our goal
- Prompt Inputs: test cases
- Solution Criteria
- Output
- Score: graded by graders
- Reasoning

TODO Are there any tools for prompt engineering? I need to improve my prompt for weekly review. Maybe start with https://langfuse.com/

Found many in <https://mirascope.com/blog/prompt-engineering-tools>, lol. It is way too competitive even for Prompt Engineering...

The first line of your prompt is the most important part of your entire request. This is where you set the stage for everything that follows, and getting it right can dramatically improve your results.

- Clarity and directness: using simple language and avoid ambiguity
  - State what you want explicitly
  - Lead your prompt with a simple statement
  - Instead of: I need to know .... Use: Write three paragraphs about ....
  - Instead of: I was reading about renewable ... Use: Identify three countries that use geothermal energy ...

Direct Instructions

- Use instructions, not questions
- Start with direct action verbs like "Write," "Create," or "Generate"

For example: Generate a one-day meal plan for an athlete that meets their dietary restrictions.

This revision immediately tells Claude:

- What action to take (generate)
- What to create (a meal plan)
- Key constraints (one day, for an athlete, meeting dietary restrictions)

TODO I think it might a good idea to train how to communcate with AI, how to write clear language to AI. Or we could create a new language for AI (like next generation programming language)

TODO create a prompt to improve your prompt and make it clear.

# 2025-07-30

## Prompt evaluation

- Model based grading: use AI model to grade
- Code based grading: Syntax validation
- Human based grading: judged by human

Main aim: 1. build a benchmark first. 2. Keep improving prompts. 3. Find the best Prompt.

Before implementing any grader, you need clear evaluation criteria. For a code generation prompt, you might focus on:

- Format - Should return only Python, JSON, or Regex without explanation
- Valid Syntax - Produced code should have valid syntax
- Task Following - Response should directly address the user's task with accurate code

```
def grade_by_model(test_case, output):
    # Create evaluation prompt
    eval_prompt = """
    You are an expert code reviewer. Evaluate this AI-generated solution.
    
    Task: {task}
    Solution: {solution}
    
    Provide your evaluation as a structured JSON object with:
    - "strengths": An array of 1-3 key strengths
    - "weaknesses": An array of 1-3 key areas for improvement  
    - "reasoning": A concise explanation of your assessment
    - "score": A number between 1-10
    """
    
    messages = []
    add_user_message(messages, eval_prompt)
    add_assistant_message(messages, "```json")
    
    eval_text = chat(messages, stop_sequences=["```"])
    return json.loads(eval_text)
```

TODO create a prompt finder app, to test my prompts

test

# 2025-07-29

## Prompt evaluation

Prompt engineering gives you techniques for writing better prompts, while prompt evaluation helps you measure how well those prompts actually work.

Prompt Engineering: crafting effective prompts
Prompt Evaluation: Automated testing to measure how well your prompts work

Combine both -> Best prompts for your application

We need to build a evaluation pipeline for scoring and improving prompts, handle corner cases from users, because users often provide unexpected inputs.

There will be a new role in the future: Prompt tester.

## Prompt Eval Workflow:

### Draft a Prompt: A basic prompt will serve as baseline for testing and improvement.

```
Please answer the user's question:

{question}
```

### Create an Eval Dataset.

```
"What's 2+2?"
"How do I make oatmeal?"
"How far away is the Moon?"
```

### Feed Through Claude

For example, the first question becomes:

```
Please answer the user's question:
What's 2+2?
```

### Feed Through a Grader

This step provides objective scoring, typically on a scale from 1 to 10, where 10 represents a perfect answer and lower scores indicate room for improvement.

In our example, the grader might assign:
```
Math question: 10 (perfect answer)
Oatmeal question: 4 (needs improvement)
Moon question: 9 (very good answer)
```
The average score across all questions gives you an objective measurement: (10 + 4 + 9) ÷ 3 = 7.66

### Change Prompt and Repeat

```
prompt = f"""
Please answer the user's question:

{question}

Answer the question with ample detail
"""
```

you might get a higher average score of 8.7, indicating that the additional instruction helped Claude provide better responses.

You can also build a version control system for prompt performance.

## Generating test datasets

Use Haiku for cheap and faster datasets generating.

## Running the eval

Load dataset, pass the item to eval function, and get results.

## Model based grading

There are three main approaches to grading model outputs:

- Code graders - Programmatically evaluate outputs using custom logic
- Model graders - Use another AI model to assess the quality
- Human graders - Have people manually review and score outputs

# 2025-07-27

## Streaming

As LLM generate words one by one, we could stream our output. Provide a better and quicker UX.

Claude sends back several types of Stream events:

- MessageStart - A new message is being sent
- ContentBlockStart - Start of a new block containing text, tool use, or other content
- ContentBlockDelta - Chunks of the actual generated text
- ContentBlockStop - The current content block has been completed
- MessageDelta - The current message is complete
- MessageStop - End of information about the current message

there are two powerful techniques for controlling Claude's output: prefilled assistant messages and stop sequences.

instead of just sending a user message, you add an assistant message at the end of your message list. Claude sees this assistant message and thinks "I've already started responding to this question, so I should continue from where I left off."

Prefilled assistant messages provide Claude a lead to continue. Claude will continue the response from there.

For example, if you ask "Is tea or coffee better at breakfast?" without prefilling, Claude typically gives a balanced response mentioning both options. But if you add an assistant message saying "Coffee is better because", Claude will continue from there and build a case for coffee.

The key thing to understand is that Claude continues from exactly where your prefilled text ends. If you write "Coffee is better because", Claude won't repeat that text - it will pick up right after "because" and complete the thought.

Stop sequences force Claude to end its response immediately when it generates a specific string of characters. 

For example, if you ask Claude to "Count from 1 to 10" with a stop sequence of "5", you'll get: 1, 2, 3, 4, 

These techniques are particularly useful for:

- Consistent formatting: Use prefilling to ensure responses always start with a specific structure
- Controlled length: Use stop sequences to cap responses at natural breakpoints
- Biased responses: When you need Claude to take a particular stance rather than being neutral
- Structured output: Combine both techniques to generate responses that fit specific templates

# 2025-07-25

## Coding

今天开始实战，所以我创建的代码不放在笔记了，放在这个仓库里面 <https://github.com/brucexu-eth/claude-courses-code>

重点笔记：

- Anthropic 是 stateless 的，所以如果你要关联上下文，就必须把上下文都提供进去。这也是为什么 claude code 需要时不时 compact 一下，因为每次发送都是整个上下文都发。所以官方提供建议从大往小，然后专注某一个具体的事情，不要同时做
- system prompts 用于设置 role 或者指示 AI 用什么用的姿态来思考和回复
- Temperature 修改选择 token 的概率，从 0 - 1，如果 0 则始终选择最高概率的那个 next word，否则就是更加随机。所以需要在不同的使用场景来选择不一样的，比如 解析数据和处理用 0，脑暴创意用 1

# 2025-07-24

## https://anthropic.skilljar.com/claude-with-the-anthropic-api

看了下课程，似乎是有几个小的 modules 然后组合在一起形成不同的课程，看了下这个应该是最全的，可以从零到一开发整个 AI 项目的，所以先 enroll 了这个课程。

看了下课程信息量好大，感觉这次学完这个就足够了。

### Claude models

- Opus: highest level of intelligence
- Sonnet: balances quality, speed, cost
- Haiku: Most cost-efficient and latency-optimized model (no resoning)

Combine together: use Opus to design the product and Sonnet to code and Haiku for user interact.

### Accessing the API

Parameters:

- API Key
- Model
- Messages
- Max Tokens: Limit to how many "tokens" the model can generate

Process of LLM:

- Tokenization: sentences -> words -> tokens
- Embedding: each token -> (-0.34, 0.87, 0.15 ...) - numbers represent all possible meanings of each words
- Contextualization: adjust embedding based on other embeddings around, because the meaning of the word might be changed in the context. The final embeddings sent to Output Layer to make predict to the next word (highest probability) -> a mix of probability and randomness to select words -> natural and varied responses -> added to the last of the list of embeddings and repeat the process
  - Have we exceeded the max tokens?
  - Did we generate a token matching a stop sequence? -> Model thinks this is the end of the response
  - Did we output an End of Sequence token?
- Generation text

API Response:

- Message
- Usage: Number of input + output tokens
- Stop Reasons: Why the model stopped generation

TODO https://anthropic.skilljar.com/claude-with-the-anthropic-api/287725
<!-- Content_END -->
