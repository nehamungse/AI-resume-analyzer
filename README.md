# AI-resume-analyzer
Built an AI Resume Analyzer using Python, LLMs, NLP, and Streamlit to automate resume screening, job matching, ATS scoring, and personalized optimization feedback.

import streamlit as st
import pdfplumber
import ollama


# -----------------------------
# Extract Text From PDF
# -----------------------------

def extract_resume_text(uploaded_file):

    text = ""

    with pdfplumber.open(uploaded_file) as pdf:

        for page in pdf.pages:
            page_text = page.extract_text()

            if page_text:
                text += page_text + "\n"

    return text



# -----------------------------
# AI Resume Analysis
# -----------------------------

def analyze_resume(resume_text, job_description):

    prompt = f"""

You are an expert ATS Resume Analyzer and Data Analyst Recruiter.

Analyze the resume according to the job description.

RESUME:

{resume_text}


JOB DESCRIPTION:

{job_description}


Provide output in this format:


# ATS Score
Give score from 0-100.


### Resume Summary


### Matching Skills


### Missing Skills


### Keyword Match Analysis


### Experience Analysis


### Project Analysis


### Resume Weaknesses


### Improvement Suggestions


### Final Hiring Recommendation

"""

    response = ollama.chat(

        model="llama3.2",

        messages=[
            {
                "role": "user",
                "content": prompt
            }
        ]

    )


    return response["message"]["content"]



# -----------------------------
# Streamlit Application
# -----------------------------


st.set_page_config(
    page_title="AI Resume Analyzer",
    page_icon="📄",
    layout="wide"
)


st.title("📄 AI Resume Analyzer")
st.write(
    "Analyze your resume against a job description using a free local AI model."
)



# Upload Resume

uploaded_file = st.file_uploader(
    "Upload Resume PDF",
    type=["pdf"]
)



# Job Description

job_description = st.text_area(
    "Paste Job Description",
    height=250
)



# Analyze Button

if st.button("Analyze Resume"):


    if uploaded_file is None:

        st.error(
            "Please upload a resume PDF"
        )


    elif job_description.strip()=="":


        st.error(
            "Please enter job description"
        )


    else:


        with st.spinner(
            "AI is analyzing your resume..."
        ):


            # Extract Resume

            resume_text = extract_resume_text(
                uploaded_file
            )


            # Analyze

            result = analyze_resume(
                resume_text,
                job_description
            )


        st.success(
            "Analysis Completed!"
        )


        st.markdown(
            result
        )


        # Download Report

        st.download_button(

            label="Download Report",

            data=result,

            file_name="resume_analysis_report.txt",

            mime="text/plain"

        )
