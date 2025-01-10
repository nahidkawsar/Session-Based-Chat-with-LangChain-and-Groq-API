# README: Session-Based Chat with LangChain and Groq API

## Overview
This script demonstrates how to use LangChain with the Groq API to manage chat sessions with session-specific histories. It showcases how to initialize the environment, set up session histories, and utilize a model for handling contextual conversations based on defined roles. The example includes a translation role ("English to Bangla Translator").

---

## Prerequisites
1. Python 3.8+
2. Required files and libraries:
   - `.env` file containing your Groq API key.
   - `requirements.txt` file for dependencies.
   - The provided `.pynb` file.
   - Libraries: `os`, `dotenv`, `langchain-groq`, `langchain-community`, `langchain-core`.

Install the required dependencies via pip:
```bash
pip install -r requirements.txt
```

---

## Steps

### 1. **Collect the Groq API Key**
- Visit the [Groq API dashboard](https://api.groq.com/) and log in with your credentials.
- Navigate to the API Key section.
- Generate a new API key and copy it.

### 2. **Prepare the Project Files**
1. Download or copy the following files into your project directory:
   - `.env` file:
     ```plaintext
     GROQ_API_KEY=your_api_key_here
     ```
     Replace `your_api_key_here` with the API key you collected from the Groq dashboard.
   - `requirements.txt` file containing:
     ```plaintext
     python-dotenv
     langchain-groq
     langchain-community
     langchain-core
     ```
   - `.pynb` file containing the script.

2. Ensure all files are in the same directory.

---

### 3. **Set Up Environment Variables**
The script loads environment variables using the `dotenv` library.
```python
from dotenv import load_dotenv
import os

# Load environment variables
load_dotenv()

# Retrieve the Groq API key
groq_api_key = os.getenv("GROQ_API_KEY")
```

---

### 4. **Run the Script**
1. Open the `.pynb` file in your preferred Jupyter Notebook or VS Code with a Python extension.
2. Ensure the necessary dependencies are installed by running:
   ```bash
   pip install -r requirements.txt
   ```
3. Execute the cells in the `.pynb` file step-by-step to:
   - Initialize the model.
   - Set up session history management.
   - Test session-specific contextual conversations.

---

### 5. **Initialize the Model**
The Groq model is initialized using the `langchain_groq` library.
```python
from langchain_groq import ChatGroq

# Initialize the ChatGroq model
model = ChatGroq(model="Gemma2-9b-It", groq_api_key=groq_api_key)
```

---

### 6. **Set Up Session History Store**
A `ChatMessageHistory` object is used to manage session-specific chat histories.
```python
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

# Dictionary to store session-specific chat histories
store = {}

# Function to retrieve or initialize chat history for a session
def get_session_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]

# Wrap the model with message history functionality
with_message_history = RunnableWithMessageHistory(model, get_session_history)
```

---

### 7. **Create a Prompt Template**
A `ChatPromptTemplate` is used to define the role and structure of interactions.
```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "Respond in such a way that you are a {role}."),
        MessagesPlaceholder(variable_name="messages"),
    ]
)

chain = prompt | model
```

---

### 8. **Wrap Model with Session-Specific History**
This step wraps the model and session history together to allow for seamless contextual conversations.
```python
from langchain_core.runnables.history import RunnableWithMessageHistory

with_message_history = RunnableWithMessageHistory(
    chain,
    get_session_history,
    input_messages_key="messages"
)
```

---

### 9. **Example Usage**
In this example, we simulate multiple inputs with a role of "English to Bangla Translator" and maintain context across a session.
```python
from langchain_core.messages import HumanMessage

config = {"configurable": {"session_id": "chat1"}}

# Interaction 1
response = with_message_history.invoke(
    {"messages": [HumanMessage(content="Hi, I am Nahid")], "role": "English to Bangla Translator"},
    config=config
)
print(response.content)

# Interaction 2
response = with_message_history.invoke(
    {"messages": [HumanMessage(content="I am From Bangladesh")], "role": "English to Bangla Translator"},
    config=config
)
print(response.content)

# Interaction 3
response = with_message_history.invoke(
    {"messages": [HumanMessage(content="I am From Noakhali Science And Technology University")], "role": "English to Bangla Translator"},
    config=config
)
print(response.content)

# Interaction 4
response = with_message_history.invoke(
    {"messages": [HumanMessage(content="What do you know about me?")], "role": "English to Bangla Translator"},
    config=config
)
print(response.content)
```

---

### 10. **Output**
The script will generate context-aware responses based on the inputs and session history, maintaining the specified role.

---

## Notes
1. Ensure your Groq API key is valid and active.
2. The session ID (`chat1`) ensures that the conversation history is maintained for that session.
3. You can modify the role or prompt template to fit different applications.

---

## Potential Enhancements
1. Add error handling for invalid API keys or missing environment variables.
2. Extend session storage to a database or cache for persistence.
3. Customize the role for use cases such as summarization, sentiment analysis, or code translation.

---

## Author
**Nahid**  
[LinkedIn Profile](https://www.linkedin.com/in/nahid-kawsar/)

