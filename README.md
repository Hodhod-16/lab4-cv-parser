# HR Candidate Profile Parser

A Streamlit application that extracts structured candidate information from a text-based PDF CV. The app sends the extracted text to a FastAPI service running in a Kaggle notebook, where a quantized Mistral-Nemo model parses it into JSON.

Built as Lab 4 of the LLMs Internship Program at Tips Hindawi, extending the CV Parser from Lab 3.

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

## Technologies

- Python, PyTorch, Hugging Face Transformers
- Mistral-Nemo-Instruct-2407 with 4-bit quantization (bitsandbytes)
- LangChain (`PyPDFLoader`, `StructuredOutputParser`)
- FastAPI and Uvicorn
- ngrok
- Streamlit

## Architecture

```text
PDF CV
  → Streamlit app
  → PyPDFLoader extracts text
  → FastAPI /parse endpoint (Bearer token)
  → Mistral-Nemo model in Kaggle
  → Structured JSON returned to Streamlit
```

## Repository files

```text
.
├── app.py
├── lab-4-cv-parser.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

## Run the API in Kaggle

1. Open `lab-4-cv-parser.ipynb` in Kaggle and enable a GPU.
2. Store `CV_API_TOKEN` and `NGROK_AUTHTOKEN` as Kaggle Secrets.
3. Run the notebook cells to load the model, define `parse_cv`, and start FastAPI.
4. Start the ngrok tunnel and copy the `/parse` endpoint URL.
5. Keep the Kaggle notebook session and ngrok tunnel running while using the app.

The notebook is saved without outputs. The API URL changes whenever the ngrok tunnel changes, so update the app's API URL when needed.

## Run the Streamlit app locally

Install the packages:

```bash
pip install -r requirements.txt
```

Start the app from the repository folder:

```bash
streamlit run app.py
```

Enter the API URL ending in `/parse` and the `CV_API_TOKEN` in the app sidebar.

## Deploy to Streamlit Community Cloud

1. Deploy this GitHub repository from Streamlit Community Cloud.
2. Add the following values in the app's Secrets settings:

```toml
CV_API_URL = "https://YOUR-NGROK-URL/parse"
CV_API_TOKEN = "YOUR_CV_API_TOKEN"
```

Do not commit tokens or secrets to GitHub.

## Tested on

Manual tests:

- A short sample CV (John Smith): the API returned valid JSON with name, email, education, skills, and experience.
- A real student CV in PDF format: name, email, education, and skills were extracted. Experience was empty because the CV lists no formal employment, which matches the parser's rule.
- A PDF of notes that is not a CV: the app showed a "no candidate information found" message instead of crashing.

Not tested yet: scanned PDFs, very long CVs, CVs in other languages, and CVs with a missing email.

## Limitations

- The Kaggle notebook and ngrok tunnel must be running for the API to respond.
- PyPDFLoader extracts available PDF text; scanned PDFs may need OCR and can produce no usable text.
- The model can miss or misinterpret information, so review extracted results before using them.
- This is a prototype. CV text is sent to a temporary public tunnel, so it is not suitable for real sensitive candidate data.
