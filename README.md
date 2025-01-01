# Nonsecure_Noted
Notes taking application with Python &amp; Flask,
NonSecureNote
A simple and intentionally insecure note-taking web application for demonstration and learning purposes. The application allows users to create, view, and delete notes using a Flask backend and an SQLite database.

Features
Create Notes: Users can write and save text-based notes.
View Notes: Previously saved notes are displayed dynamically.
Delete Notes: Users can delete individual notes from the application.
Insecure by Design:
Confidentiality: No encryption for data transmission or storage.
Integrity: Susceptible to SQL Injection attacks.
Availability: Lacks input size validation and rate-limiting mechanisms.
Technologies Used
Backend: Python (Flask)
Frontend: HTML, CSS, JavaScript
Database: SQLite
Prerequisites
Make sure you have the following installed:

Python 3.x
pip (Python package manager)
Setup Instructions
Clone the Repository
bash
Kopier kode
git clone https://github.com/your-username/nonsecure-note.git
cd nonsecure-note
Install Dependencies
Install the required Python packages using pip:

bash
Kopier kode
pip install flask
Run the Application
Start the Flask server:
bash
Kopier kode
python secure.py
Open your browser and navigate to:
arduino
Kopier kode
http://127.0.0.1:5000
File Structure
bash
Kopier kode
NonSecureNote/
├── templates/
│   └── index.html         # Frontend HTML file
├── secure.py              # Flask backend
├── nonsecure_note.db      # SQLite database (automatically created)
Usage
Navigate to the application in your browser.
Create notes using the provided form.
View your saved notes in the "Notes" section.
Delete notes as needed using the "Delete" button.
Intentionally Introduced Vulnerabilities
This application is designed to be insecure for educational purposes:

Confidentiality:
No HTTPS or encryption for data transmission.
Notes are stored in plaintext in the SQLite database.
Integrity:
SQL queries are vulnerable to SQL Injection attacks.
Availability:
No validation for input sizes.
No rate-limiting, making it prone to DoS attacks.
Disclaimer
This project is not intended for production use. It is developed for learning purposes to demonstrate insecure coding practices. Do not deploy this application in







Du sagde:
just write short notes 
ChatGPT sagde:
ChatGPT
NonSecureNote
A simple and intentionally insecure note-taking web application built with Flask and SQLite.

Features
Create, view, and delete text notes.
Simple web UI with dynamic updates.
Designed with vulnerabilities for educational purposes.
Technologies
Backend: Python (Flask)
Frontend: HTML, CSS, JavaScript
Database: SQLite
Setup
Clone the repository:
bash
Kopier kode
git clone https://github.com/your-username/nonsecure-note.git
cd nonsecure-note
Install dependencies:
bash
Kopier kode
pip install flask
Run the app:
bash
Kopier kode
python secure.py
Access at: http://127.0.0.1:5000
Disclaimer
This app is intentionally insecure and for educational purposes only. Do not use in production.






