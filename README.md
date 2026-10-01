{
  "name": "Resume Screening (Manual)",
  "nodes": [
    {
      "parameters": {},
      "id": "67d5413e-ae76-46b5-a40f-41089912a9a0",
      "name": "Start",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [
        0,
        112
      ]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "i1",
              "name": "job_description",
              "value": "Backend Data Engineer: Python, SQL, Docker, 3+ years experience.",
              "type": "string"
            },
            {
              "id": "i2",
              "name": "resume_url",
              "value": "data:text/html,<html><head><title>Akshaya - Python Developer Resume</title></head><body style=\"font-family:Arial;max-width:800px;margin:40px auto;line-height:1.5\"><h1>AKSHAYA</h1><h2>Python Developer</h2><p>Guntur, Andhra Pradesh, India | +91 98765 43210 | akshaya@example.com</p><hr><h2>Professional Summary</h2><p>Motivated and detail-oriented Python Developer with experience developing applications, automation scripts, REST APIs, and database-driven solutions. Skilled in Python, Django, Flask, SQL, Git, and Airtable.</p><h2>Technical Skills</h2><ul><li>Python, SQL, JavaScript</li><li>Django, Flask, FastAPI</li><li>MySQL, PostgreSQL, SQLite</li><li>Git, GitHub, Docker, Postman</li><li>Airtable and Airtable API</li><li>REST API Development</li><li>Python Automation and Data Processing</li></ul><h2>Professional Experience</h2><h3>Python Developer - Tech Solutions Pvt. Ltd.</h3><p>Hyderabad, India | June 2023 - Present</p><ul><li>Developed Python applications and automation scripts.</li><li>Built REST APIs using Flask and FastAPI.</li><li>Worked with MySQL and PostgreSQL databases.</li><li>Integrated Airtable with internal business workflows.</li><li>Created automated data-processing scripts using Python.</li><li>Used Git and GitHub for version control.</li></ul><h3>Junior Python Developer - CodeWorks Technologies</h3><p>Bengaluru, India | July 2021 - May 2023</p><ul><li>Developed Python modules for web applications.</li><li>Created SQL queries and database operations.</li><li>Developed REST APIs using Flask.</li><li>Automated reporting and data-entry tasks.</li><li>Maintained Airtable records and workflows.</li></ul><h2>Projects</h2><h3>Customer Management System</h3><p>Developed a Python-based customer management application using Django and PostgreSQL.</p><h3>Airtable Data Automation System</h3><p>Created a Python automation workflow using the Airtable API to process and synchronize business data.</p><h2>Education</h2><p><b>Bachelor of Technology - Computer Science</b><br>JNTU, Andhra Pradesh | 2017 - 2021<br>CGPA: 8.2/10</p><h2>Certifications</h2><ul><li>Python Programming Certification</li><li>Django Web Development</li><li>SQL and Database Management</li><li>REST API Development</li></ul><h2>Strengths</h2><p>Problem Solving | Logical Thinking | Team Collaboration | Quick Learning | Communication</p><h2>Languages</h2><p>English | Telugu | Hindi</p><h2>Declaration</h2><p>I hereby declare that the information provided above is true and accurate to the best of my knowledge.</p><p><b>Akshaya</b></p></body></html>",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "588d4e8f-578c-4c88-bb8d-dac48f36cb88",
      "name": "Job Inputs",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        336,
        112
      ]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "t1",
              "name": "text",
              "value": "={{ $json.resume_url.replace(/^data:text\\/html,/, \"\").replace(/<(br|\\/p|\\/li|\\/h[1-6]|hr)[^>]*>/gi, \"\\n\").replace(/<[^>]+>/g, \" \").replace(/[ \\t]+/g, \" \").replace(/\\n\\s*/g, \"\\n\").trim() }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "66b94e7e-7146-4cca-9064-0e26f80f385b",
      "name": "Extract Resume Text",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        672,
        112
      ]
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "=Job description:\n{{ $('Job Inputs').item.json.job_description }}\n\nCandidate resume:\n{{ $json.text }}",
        "hasOutputParser": true,
        "messages": {
          "messageValues": [
            {
              "message": "You are a recruiting assistant. Compare the resume against the job description. Extract the candidate name, email, phone, total years of experience, all skills, and highest education. matching_skills = skills required by the job that the candidate has; missing_skills = skills required by the job that the candidate lacks. Give a match_score from 0 to 100. recommendation: \"Shortlist\" for score 85+, \"Review\" for 60-84, \"Reject\" below 60. reasoning: 1-3 sentences. Use only facts from the resume; use an empty string or 0 when unknown."
            }
          ]
        },
        "batching": {}
      },
      "id": "d15e58a6-c9e5-4ed1-b024-cd0043e2fb02",
      "name": "Screen Candidate",
      "type": "@n8n/n8n-nodes-langchain.chainLlm",
      "typeVersion": 1.9,
      "position": [
        896,
        112
      ]
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "mode": "list",
          "value": "gpt-5.4-mini"
        },
        "builtInTools": {},
        "options": {}
      },
      "id": "6ffc9a27-8b4f-43c7-b53c-71fe44472391",
      "name": "Screening Model",
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.3,
      "position": [
        896,
        336
      ],
      "credentials": {
        "openAiApi": {
          "id": null,
          "name": "Gateway credits",
          "__aiGatewayManaged": true
        }
      }
    },
    {
      "parameters": {
        "schemaType": "manual",
        "inputSchema": "{\n  \"type\": \"object\",\n  \"properties\": {\n    \"candidate_name\": { \"type\": \"string\" },\n    \"email\": { \"type\": \"string\" },\n    \"phone\": { \"type\": \"string\" },\n    \"years_experience\": { \"type\": \"number\" },\n    \"skills\": { \"type\": \"array\", \"items\": { \"type\": \"string\" } },\n    \"matching_skills\": { \"type\": \"array\", \"items\": { \"type\": \"string\" } },\n    \"missing_skills\": { \"type\": \"array\", \"items\": { \"type\": \"string\" } },\n    \"education\": { \"type\": \"string\" },\n    \"match_score\": { \"type\": \"number\" },\n    \"recommendation\": { \"type\": \"string\", \"enum\": [\"Shortlist\", \"Review\", \"Reject\"] },\n    \"reasoning\": { \"type\": \"string\" }\n  },\n  \"required\": [\"candidate_name\", \"email\", \"phone\", \"years_experience\", \"skills\", \"matching_skills\", \"missing_skills\", \"education\", \"match_score\", \"recommendation\", \"reasoning\"]\n}"
      },
      "id": "e4c37440-46e4-450f-a66c-ecae75b5bbfc",
      "name": "Screening Result Parser",
      "type": "@n8n/n8n-nodes-langchain.outputParserStructured",
      "typeVersion": 1.3,
      "position": [
        1040,
        336
      ]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "f1",
              "name": "Candidate Name",
              "value": "={{ $json.output.candidate_name }}",
              "type": "string"
            },
            {
              "id": "f2",
              "name": "Email",
              "value": "={{ $json.output.email }}",
              "type": "string"
            },
            {
              "id": "f3",
              "name": "Phone",
              "value": "={{ $json.output.phone }}",
              "type": "string"
            },
            {
              "id": "f4",
              "name": "Years Experience",
              "value": "={{ $json.output.years_experience }}",
              "type": "number"
            },
            {
              "id": "f5",
              "name": "Skills",
              "value": "={{ ($json.output.skills || []).join(\", \") }}",
              "type": "string"
            },
            {
              "id": "f6",
              "name": "Matching Skills",
              "value": "={{ ($json.output.matching_skills || []).join(\", \") }}",
              "type": "string"
            },
            {
              "id": "f7",
              "name": "Missing Skills",
              "value": "={{ ($json.output.missing_skills || []).join(\", \") }}",
              "type": "string"
            },
            {
              "id": "f8",
              "name": "Education",
              "value": "={{ $json.output.education }}",
              "type": "string"
            },
            {
              "id": "f9",
              "name": "Match Score",
              "value": "={{ $json.output.match_score }}",
              "type": "number"
            },
            {
              "id": "f10",
              "name": "Recommendation",
              "value": "={{ $json.output.recommendation }}",
              "type": "string"
            },
            {
              "id": "f11",
              "name": "AI Reasoning",
              "value": "={{ $json.output.reasoning }}",
              "type": "string"
            },
            {
              "id": "f12",
              "name": "Screened At",
              "value": "={{ $now.toFormat(\"yyyy-MM-dd HH:mm\") }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "7bdd5f28-74d0-4c76-8dfc-01a11f94b7db",
      "name": "Format Result",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        1248,
        112
      ]
    }
  ],
  "pinData": {},
  "connections": {
    "Start": {
      "main": [
        [
          {
            "node": "Job Inputs",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Job Inputs": {
      "main": [
        [
          {
            "node": "Extract Resume Text",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract Resume Text": {
      "main": [
        [
          {
            "node": "Screen Candidate",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Screen Candidate": {
      "main": [
        [
          {
            "node": "Format Result",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Screening Model": {
      "ai_languageModel": [
        [
          {
            "node": "Screen Candidate",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Screening Result Parser": {
      "ai_outputParser": [
        [
          {
            "node": "Screen Candidate",
            "type": "ai_outputParser",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate"
  },
  "versionId": "8bab640a-d455-498f-8729-06a310718db3",
  "meta": {
    "instanceId": "f10a941e5915407efad3350cd9aaae78073a8402ae85ac432d6998cbedc9afb7"
  },
  "nodeGroups": [],
  "id": "QXJ1bnELIXMmjsym",
  "tags": []
}
