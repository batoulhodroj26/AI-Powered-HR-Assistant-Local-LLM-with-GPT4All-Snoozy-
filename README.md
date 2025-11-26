# AI-Powered-HR-Assistant-Local-LLM-with-GPT4All-Snoozy-
This project implements an AI-powered HR Assistant that can answer employee questions, retrieve details from a mock database, check leave balances, and generate interview questions. Unlike typical OpenAI API-based solutions, this project runs entirely locally using GPT4All Snoozy, so no API key or online calls are required. 
Features

✅ Answer general HR-related questions.
✅ Retrieve employee details from a mock database.
✅ Check leave balances for employees.
✅ Generate interview questions based on job roles.
✅ Fully local — uses GPT4All Snoozy, no OpenAI API required.
✅ Interactive Gradio-based UI.

Demo / Example Usage

Once running, you can try:

Question:

What is my leave balance? My employee ID is E12345


Assistant:

You have 12 leave days remaining.


Question:

Get employee details for E12345


Assistant:

Name: Alice Smith, Department: Engineering, Role: Software Engineer


Question:

Generate interview questions for a Data Scientist


Assistant:

1. Explain the difference between supervised and unsupervised learning.
2. How do you handle missing data in a dataset?
3. What is overfitting and how do you prevent it?

Project Structure
AI-Powered-HR-Assistant/
│
├── main.py                # Main chatbot logic + Gradio UI
├── hr_tools.py            # Mocked HR tools (employee details, leave, interview questions)
├── config.py              # Configuration file (for API keys if needed)
├── models/                # Folder to store GPT4All model(s)
│   └── gpt4all-snoozy.bin
├── README.md              # Project documentation
└── requirements.txt       # Python dependencies

Installation Steps

Clone the repository:

git clone https://github.com/<your-username>/AI-Powered-HR-Assistant.git
cd AI-Powered-HR-Assistant


Install Python dependencies:

pip install -U gpt4all langchain gradio


Download GPT4All Snoozy model:

Go to https://gpt4all.io/models

Download GPT4All Snoozy for Windows (.bin)

Place it inside models/ folder in the project:

E:\AI-Powered HR Assistant\models\gpt4all-snoozy.bin


Update model path in main.py if needed:

model_path = "models/gpt4all-snoozy.bin"

Running the Project
python main.py


Gradio will open a local URL (e.g., http://127.0.0.1:7860)

Type your HR-related questions and interact with the assistant.

HR Tools Implemented

get_employee_details(employee_id: str) -> dict

check_leave_balance(employee_id: str) -> dict

generate_interview_questions(job_role: str) -> list

These tools use mocked data, which can be extended or replaced with a real database.

Why GPT4All Snoozy?

Fully local — no internet or OpenAI API required.

Lightweight and fast for testing projects.

Ideal for school/portfolio projects where API usage is limited.

Future Enhancements / TODO

Multi-turn conversation memory

Streaming responses in Gradio

Add new HR tools (e.g., company policies, salary queries)

Web deployment using Flask or FastAPI

Dependencies

Python 3.10+

gpt4all

langchain

gradio

You can install all dependencies with:

pip install -r requirements.txt


Example requirements.txt:

gpt4all
langchain
gradio
hr_tools.py
# hr_tools.py
# Mocked HR functions

def get_employee_details(employee_id: str) -> dict:
    # Mocked employee database
    employees = {
        "E12345": {"name": "Alice Smith", "department": "Engineering", "role": "Software Engineer"},
        "E67890": {"name": "Bob Johnson", "department": "HR", "role": "HR Manager"},
    }
    return employees.get(employee_id, {"error": "Employee not found"})

def check_leave_balance(employee_id: str) -> dict:
    # Mocked leave balances
    leave_balances = {
        "E12345": {"remaining_leave_days": 12},
        "E67890": {"remaining_leave_days": 8},
    }
    return leave_balances.get(employee_id, {"error": "Employee not found"})

def generate_interview_questions(job_role: str) -> list:
    # Mocked interview questions per role
    questions = {
        "Data Scientist": [
            "Explain supervised vs unsupervised learning.",
            "How do you handle missing data?",
            "What is overfitting and how do you prevent it?"
        ],
        "Software Engineer": [
            "What is OOP?",
            "Explain the difference between REST and GraphQL.",
            "What is a deadlock in multithreading?"
        ]
    }
    return questions.get(job_role, ["No questions available for this role"])

2️⃣ main.py
# main.py
from gpt4all import GPT4All
import gradio as gr
import json
from hr_tools import get_employee_details, check_leave_balance, generate_interview_questions

# Load GPT4All Snoozy model
model_path = "models/gpt4all-snoozy.bin"  # Update if your path is different
llm = GPT4All(model_path)

TOOLS = {
    "get_employee_details": get_employee_details,
    "check_leave_balance": check_leave_balance,
    "generate_interview_questions": generate_interview_questions
}

def parse_tool_call(response_text: str):
    """
    Parse JSON tool call from model response if present
    """
    try:
        start = response_text.index("{")
        end = response_text.rindex("}") + 1
        return json.loads(response_text[start:end])
    except:
        return None

def hr_assistant(user_input: str):
    """
    Main HR assistant function
    """
    prompt = f"""
You are an HR assistant. Answer questions. If a question requires a tool call, output JSON with "name" and "arguments".

Question: {user_input}
"""
    response = llm.generate(prompt)
    text = response.text if hasattr(response, "text") else response

    tool_call = parse_tool_call(text)
    if tool_call:
        tool_name = tool_call.get("name")
        args = tool_call.get("arguments", {})
        if tool_name in TOOLS:
            result = TOOLS[tool_name](**args)
            return f"Tool result: {result}"
        else:
            return f"❌ Unknown tool '{tool_name}'"
    else:
        return text

iface = gr.Interface(
    fn=hr_assistant,
    inputs="text",
    outputs="text",
    title="AI-Powered HR Assistant (GPT4All Snoozy)",
    description="Ask about leave balance, employee details, or interview questions."
)

if __name__ == "__main__":
    iface.launch()

3️⃣ requirements.txt
gpt4all
langchain
gradio

✅ How to run

Make sure you have Python 3.10+ installed.

Install dependencies:

pip install -r requirements.txt


Make a folder models/ in your project directory and put the downloaded gpt4all-snoozy.bin inside.

Run the assistant:

python main.py


Open the Gradio URL (e.g., http://127.0.0.1:7860) and ask:

What is my leave balance? My employee ID is E12345


✅ It will return the mocked result locally, no API key needed.
