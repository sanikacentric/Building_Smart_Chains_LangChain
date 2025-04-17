LangChain simplifies the complexity of working with LLMs by offering elegant abstractions like PromptTemplate, ChatPromptTemplate, and powerful chaining using LCEL (LangChain Expression Language). In this article, we’ll explore the difference between single-turn and multi-turn prompting, and dive into chaining concepts—both sequential and parallel—to build smart, modular AI workflows.

🧠 PromptTemplate vs ChatPromptTemplate
LangChain provides two major ways to construct prompts depending on the model type:

✴️ PromptTemplate
Purpose: Create prompts for non-chat (completion) models like text-davinci-003.

Output: A single formatted string.

Use Case: One-shot instructions like:

pgsql
Copy
Edit
Translate the following sentence to French: {text}
python
Copy
Edit
from langchain.prompts import PromptTemplate

prompt = PromptTemplate(
    template="Can you give me a detailed explanation of {topic}?",
    input_variables=["topic"]
)
💬 ChatPromptTemplate
Purpose: Designed for chat-based models like gpt-3.5-turbo, gpt-4.

Output: A list of structured messages (e.g., system, human, assistant).

Use Case: Conversations where roles are defined:

python
Copy
Edit
from langchain.prompts import ChatPromptTemplate

chat_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("human", "Explain the concept of {topic}")
])
Supports message roles:

🧑‍💻 Human

🤖 AI

⚙️ System

🔗 Chaining with LCEL (LangChain Expression Language)
LangChain’s Runnable interface lets you pipe components together using the | operator, creating chains that are expressive and composable.

✅ Basic Sequential Chain
Step 1: Define Prompt
python
Copy
Edit
from langchain.prompts import PromptTemplate
from langchain_core.output_parsers import StrOutputParser

prompt = PromptTemplate(
    template="Can you give me a detailed explanation of {topic}?",
    input_variables=["topic"]
)
Step 2: Define LLM and Parser
python
Copy
Edit
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI()
parser = StrOutputParser()
Step 3: Chain Using Pipe Operator
python
Copy
Edit
chain = prompt | llm | parser
response = chain.invoke({"topic": "machine learning"})
This uses the invoke method from the Runnable class to get results.

🔁 Building Sequential Chains
Let’s build a chain that first generates a detailed report, then summarizes it into 3 points.

python
Copy
Edit
prompt1 = PromptTemplate(
    template="Get a detailed report on {topic}",
    input_variables=["topic"]
)

prompt2 = PromptTemplate(
    template="Generate a 3-point summary from the following: {text}",
    input_variables=["text"]
)

chain = prompt1 | llm | parser | prompt2 | llm | parser
result = chain.invoke({"topic": "artificial intelligence"})
🧠 How it works:

prompt1 generates a report.

The report flows into prompt2 to generate a summary.

Each step uses the same LLM and parser in sequence.

⚡ Building Parallel Chains
Parallel chains allow you to run multiple prompts simultaneously and then combine their results.

Define Prompts
python
Copy
Edit
prompt1 = PromptTemplate(
    template="Generate a simple summary from the following text:\n{text}",
    input_variables=["text"]
)

prompt2 = PromptTemplate(
    template="Generate 3 question-answer pairs from the following text:\n{text}",
    input_variables=["text"]
)

prompt3 = PromptTemplate(
    template="Analyze the summary and Q&A to create a 5-question quiz with 4 options each.\nSummary: {summary}\nQ&A: {qa}",
    input_variables=["summary", "qa"]
)
Combine with RunnableParallel
python
Copy
Edit
from langchain.schema.runnable import RunnableParallel

# Define subchains
summary_chain = prompt1 | llm | parser
qa_chain = prompt2 | llm | parser

# Run summary and QA in parallel
parallel_chain = RunnableParallel(summary=summary_chain, qa=qa_chain)

# Merge outputs into final quiz generator
merge_chain = prompt3 | llm | parser

# Final end-to-end chain
full_chain = parallel_chain | merge_chain

result = full_chain.invoke({"text": "LangChain is an open-source framework for LLM apps..."})
📈 Visualizing the Chains
To visualize LCEL graphs, install graphviz or graldeaf:

bash
Copy
Edit
pip install graphviz
These tools help debug and understand the flow of your pipeline visually—especially helpful for longer chains.

🧩 Conclusion
LangChain’s PromptTemplate and ChatPromptTemplate give you flexibility to design for different model types. Combined with LCEL’s chaining ability, you can build powerful LLM workflows—sequentially or in parallel—with clean and readable code.

