# HR Candidate Profile Parser

A Streamlit application that extracts structured candidate information from a text-based PDF CV. The app sends the extracted text to a FastAPI service running in a Kaggle notebook, where a quantized Mistral-Nemo model parses it into JSON.

## Features

- Upload a PDF CV.
- Extract text using LangChain's `PyPDFLoader`.
- Parse the CV into a structured profile.
- View the candidate information and raw JSON.
- Download the result as a JSON file.
- Use a Bearer token to authenticate API requests.

## Extracted fields

- Full name
- Email
- Education
- Skills
- Experience

Missing information is returned as an empty string or an empty list. The parser does not count projects, coursework, volunteering, or student organizations as formal employment experience.

## Architecture
```text
PDF CV
  → Streamlit app
  → PyPDFLoader extracts text
  → FastAPI /parse endpoint
  → Mistral-Nemo model in Kaggle
  → Structured JSON returned to Streamlit
```
## Repository files
```text
.
├── app.py
├── requirements.txt
├── .gitignore
└── README.md
```
## Run the API in Kaggle
1. Open the Kaggle notebook and enable a GPU.
2. Run the notebook cells to load the model, define parse_cv, and start FastAPI.
3. Store CV_API_TOKEN and NGROK_AUTHTOKEN as Kaggle Secrets.
4. Start the ngrok tunnel and copy the /parse endpoint URL.
5. Keep the Kaggle notebook session and ngrok tunnel running while using the app.

The API URL changes when the ngrok tunnel changes. Update the app's API URL when needed.

## Run the Streamlit app locally
Install the packages:
```bash
pip install -r requirements.txt
```

Start the app from the repository folder:
```bash
streamlit run app.py
```

Enter the API URL ending in /parse and the CV_API_TOKEN in the app sidebar.

## Deploy to Streamlit Community Cloud

1. Deploy this GitHub repository from Streamlit Community Cloud.
2. Add the following values in the app's Secrets settings:
```toml
CV_API_URL = "https://YOUR-NGROK-URL/parse"
CV_API_TOKEN = "YOUR_CV_API_TOKEN"
```

Do not commit tokens or secrets to GitHub.
## Limitations
- The Kaggle notebook and ngrok tunnel must be running for the API to respond.
- PyPDFLoader extracts available PDF text; scanned PDFs may need OCR and can produce no usable text.
- The model can miss or misinterpret information, so review extracted results before using them.
