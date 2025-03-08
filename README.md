Natural Language to PostgreSQL Query Generator
This project provides a framework for translating natural language queries into executable PostgreSQL statements using the Groq API with the LLaMA-3.3-70B model.
Overview
The system takes natural language descriptions of data queries and automatically generates corresponding SQL queries that can be executed against a PostgreSQL database. It also includes functionality for correcting improperly formatted SQL statements.
Features

Convert natural language queries into executable PostgreSQL statements
Correct malformed or syntactically incorrect SQL queries
Track token usage and processing time for API calls
Handle batch processing of multiple queries

Requirements

Python 3.6+
requests library
Groq API key (sign up at Groq)

Installation

Clone this repository
Install the required packages:
Copypip install requests

Add your Groq API key to the configuration

Usage
Configuration
Replace the placeholder API key in the code with your actual Groq API key:
pythonCopyapi_key = "your_actual_api_key"  # Replace with your actual Groq API key
Input Format
The system accepts input files in JSON format:
For SQL generation:
jsonCopy[
  {
    "NL": "Find all customers who made purchases over $500 in the last month"
  },
  {
    "NL": "Get the top 10 products by sales volume"
  }
]
For SQL correction:
jsonCopy[
  {
    "IncorrectQuery": "SELECT customer_name FROM customers WERE total_purchases > 1000",
    "NL": "Find customers with purchases exceeding $1000"
  }
]
Running the Program
pythonCopypython main.py
This will process the input files, generate or correct SQL statements as needed, and save the results to output files.
Output Format
For SQL generation:
jsonCopy[
  {
    "NL": "Find all customers who made purchases over $500 in the last month",
    "Query": "SELECT c.customer_id, c.name, c.email FROM customers c JOIN orders o ON c.customer_id = o.customer_id WHERE o.total_amount > 500 AND o.order_date >= NOW() - INTERVAL '1 month' GROUP BY c.customer_id, c.name, c.email;"
  }
]
For SQL correction:
jsonCopy[
  {
    "IncorrectQuery": "SELECT customer_name FROM customers WERE total_purchases > 1000",
    "CorrectQuery": "SELECT customer_name FROM customers WHERE total_purchases > 1000"
  }
]
Prompt Engineering
The system uses carefully engineered prompts to get the best results from the language model:
CopyYou are a PostgreSQL expert tasked with converting natural language queries into precise, executable PostgreSQL statements.

Given the following database schema:
[DATABASE SCHEMA DETAILS]

Convert this natural language request into a PostgreSQL query:
"[NATURAL LANGUAGE QUERY]"

Requirements:
- Generate only the executable PostgreSQL code without explanations
- Use proper PostgreSQL syntax and features
- Include appropriate joins when relationships between tables are implied
- Apply proper filtering based on the conditions in the request
- Format the query with proper indentation for readability
- Use aliases for table names when appropriate
- Include any necessary CAST operations for data type compatibility
- Optimize the query where possible (e.g., using appropriate indexes)
- Consider performance implications for large datasets

Output only the PostgreSQL query, nothing else.
Fine-tuning the System
To improve the accuracy of SQL generation:

Include detailed database schema information in your prompts
Adjust the temperature parameter (lower for more predictable outputs)
Increase max_tokens if generating complex queries
Add examples of expected output format for more consistent results

Performance Tracking
The system tracks:

Total tokens used by the API
Time taken to generate SQL statements
Time taken to correct SQL statements

Limitations

The quality of SQL generation depends on the clarity of natural language queries
Complex queries with ambiguous language may require manual review
Schema information should be updated if database structure changes
