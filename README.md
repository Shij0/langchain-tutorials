# 🚀 FastAPI with LangChain Agent: Virtual Environment & Requirements Setup


## 📁 Updated Project Structure

```
my-langchain-api/
├── venv/                    # Virtual environment folder
├── app/
│   ├── __init__.py
│   └── main.py
└── requirements.txt         # Project dependencies
```
<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/82d3ce58-22ef-42cd-8ffb-af53aaa3f7f8" />


## 🛠️ Step-by-Step Implementation with Virtual Environment

### **Step 1: Create Project Folder and Virtual Environment**

```bash
# Create project folder
mkdir my-langchain-api
cd my-langchain-api

# Create virtual environment
# Windows:
python -m venv venv
# macOS/Linux:
python3 -m venv venv
```

### **Step 2: Activate Virtual Environment**

```bash
# Windows (Command Prompt):
venv\Scripts\activate.bat

# Windows (PowerShell):
venv\Scripts\Activate.ps1

# macOS/Linux:
source venv/bin/activate
```

> ⚠️ **Important**: Always activate the virtual environment before installing packages or running your application 【turn0search7】.

### **Step 3: Create requirements.txt File**

Create a `requirements.txt` file in your project root with the following content:

```txt
fastapi==0.135.2
uvicorn[standard]==0.42.0
langchain==1.2.13
langchain-google-genai==4.2.1
langchain-core==1.2.23
```

<details>
<summary>🔧 Alternative: Generate requirements.txt automatically</summary>

After installing packages in your virtual environment, you can generate the requirements file:

```bash
# Generate requirements.txt with exact versions
pip freeze > requirements.txt

# Or use pipreqs for minimal requirements (only what you import)
pip install pipreqs
pipreqs . --force
```

The `pip freeze` method lists all installed packages with exact versions, while `pipreqs` scans your code to include only necessary dependencies 【turn0search11】.

</details>

### **Step 4: Install Dependencies from requirements.txt**

```bash
# Install all dependencies at once
pip install -r requirements.txt
```

### **Step 5: Setup Google Cloud Authentication (ADC)**

```bash
# Install gcloud CLI if not already installed https://docs.cloud.google.com/sdk/docs/install-sdk
# Then authenticate with Application Default Credentials
gcloud auth application-default login
```

### **Step 6: Create Application Code**

Create the `app` folder and `main.py` file:

```bash
# Create app directory
mkdir app

# Create __init__.py (makes it a Python package)
# Windows:
type nul > app\__init__.py
# macOS/Linux:
touch app/__init__.py
```

Create `app/main.py` with this enhanced code:

```python

from fastapi import FastAPI
from langchain.agents import create_agent
from langchain_core.tools import tool
from langchain_google_genai import ChatGoogleGenerativeAI


app = FastAPI();

llm = ChatGoogleGenerativeAI(model="gemini-2.5-pro",
                             project="<Project_name>",
                             vertexai=True);
@tool
def calculate(expression: str) -> str:
    """Evaluate a mathematical expression and return the result."""
    return str(eval(expression));

@tool
def get_current_time() -> str:
    """Return the current time."""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S");


# create agent
agent = create_agent(model=llm,
                     system_prompt="You are a helpful assistant that can answer questions and perform calculations. Use the provided tools when necessary.",
                     tools=[calculate, get_current_time]
                     );


@app.get("/")
def root():
    return "Welcome to the Vertex AI Agent API! Use the /ask endpoint to ask questions."

@app.get("/ask")
def ask(question:str):

    input = {"messages":[{"role": "user", "content": question}]};
    response = agent.invoke(input);

    final = response["messages"][-1].content;
    return {"question": question, "answer": final}


```

### **Step 7: Run the Application**

```bash
# Run from the project root directory
# Note the app.main:app structure
python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### **Step 8: Test the API**

Visit `http://localhost:8000/docs` and test with:
- `What is 25 * 4 + 10?`
- `What time is it now?`
- `Calculate 100 / 5`
