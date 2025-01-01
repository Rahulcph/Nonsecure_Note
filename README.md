from flask import Flask, request, jsonify, render_template
import sqlite3

# Initialize Flask app
app = Flask(__name__)

# Initialize SQLite database
def init_db():
    conn = sqlite3.connect('nonsecure_note.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS notes (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    content TEXT NOT NULL,
                    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)''')
    conn.commit()
    conn.close()

init_db()  # Create database if not already initialized

@app.route('/')
def home():
    # Render the homepage
    return render_template('index.html')

@app.route('/notes', methods=['POST'])
def create_note():
    # Intentionally insecure query (SQL Injection vulnerability)
    content = request.form.get('content')  # Unsafe input handling
    conn = sqlite3.connect('nonsecure_note.db')
    c = conn.cursor()
    # Directly inserting user input without sanitization
    c.execute(f"INSERT INTO notes (content) VALUES ('{content}')")
    conn.commit()
    conn.close()
    return jsonify({"message": "Note created successfully!"}), 201

@app.route('/notes', methods=['GET'])
def get_notes():
    # Retrieve all notes
    conn = sqlite3.connect('nonsecure_note.db')
    c = conn.cursor()
    c.execute("SELECT id, content FROM notes")
    notes = c.fetchall()
    conn.close()
    return jsonify(notes)

@app.route('/notes/<int:note_id>', methods=['GET'])
def get_note_by_id(note_id):
    # Retrieve a specific note by ID
    conn = sqlite3.connect('nonsecure_note.db')
    c = conn.cursor()
    c.execute(f"SELECT id, content FROM notes WHERE id = {note_id}")  # SQL Injection vulnerability
    note = c.fetchone()
    conn.close()
    if note:
        return jsonify(note)
    else:
        return jsonify({"error": "Note not found"}), 404

@app.route('/notes/<int:note_id>', methods=['DELETE'])
def delete_note_by_id(note_id):
    # Delete a note by ID
    conn = sqlite3.connect('nonsecure_note.db')
    c = conn.cursor()
    c.execute(f"DELETE FROM notes WHERE id = {note_id}")  # SQL Injection vulnerability
    conn.commit()
    conn.close()
    return jsonify({"message": "Note deleted successfully!"})

@app.route('/notes/share/<int:note_id>', methods=['GET'])
def share_note_by_id(note_id):
    # Share a specific note by generating a shareable link
    return jsonify({"share_link": f"http://127.0.0.1:5000/notes/{note_id}"}), 200

if __name__ == '__main__':
    app.run(debug=True)  # Running the app in debug mode (not secure)






# HTML

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NonSecureNote</title>
    <style>
        /* General Styles */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f9;
            color: #333;
        }
        header {
            background-color: darkcyan;
            color: white;
            padding: 20px;
            text-align: center;
        }
        h1, h2 {
            margin-bottom: 10px;
        }

        /* Form Styling */
        form {
            background-color: #fff;
            padding: 20px;
            margin: 20px auto;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            width: 80%;
            max-width: 500px;
        }
        textarea {
            width: 100%;
            border: 1px solid #ccc;
            border-radius: 5px;
            padding: 10px;
            margin-bottom: 10px;
            font-size: 1em;
        }
        button {
            background-color: yellowgreen;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }

        /* Notes Section */
        #notes {
            margin: 20px auto;
            width: 80%;
            max-width: 500px;
        }
        .note {
            background-color: #fff;
            margin-bottom: 10px;
            padding: 10px;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            position: relative;
        }
        .note p {
            margin: 0;
        }
        .note small {
            color: #555;
        }
        .delete-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: #ff4d4d;
            color: white;
            border: none;
            padding: 5px 10px;
            font-size: 0.9em;
            border-radius: 5px;
            cursor: pointer;
        }
        .delete-btn:hover {
            background-color: #cc0000;
        }
    </style>
</head>
<body>
    <header>
        <h1>Welcome to NonSecureNote</h1>
    </header>

    <section>
        <h2 style="text-align: center;">Create a Note</h2>
        <form id="noteForm">
            <textarea name="content" id="noteContent" rows="4" placeholder="Write your note here..."></textarea><br>
            <button type="submit">Save Note</button>
        </form>
    </section>

    <section>
        <h2 style="text-align: center;">Notes</h2>
        <div id="notes">
            <!-- Notes will be dynamically displayed here -->
        </div>
    </section>

    <script>
        async function fetchNotes() {
            const response = await fetch('/notes');
            const notes = await response.json();
            const notesDiv = document.getElementById('notes');
            notesDiv.innerHTML = '';
            notes.forEach(note => {
                const noteDiv = document.createElement('div');
                noteDiv.className = 'note';
                noteDiv.innerHTML = `
                    <p>${note[1]}</p>
                    <small>Note ID: ${note[0]}</small>
                    <button class="delete-btn" onclick="deleteNote(${note[0]})">Delete</button>
                `;
                notesDiv.appendChild(noteDiv);
            });
        }

        document.getElementById('noteForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            const content = document.getElementById('noteContent').value;

            if (!content.trim()) {
                alert('Please write something before saving!');
                return;
            }

            const response = await fetch('/notes', {
                method: 'POST',
                headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
                body: new URLSearchParams({ content })
            });

            if (response.ok) {
                document.getElementById('noteContent').value = '';
                fetchNotes();
            } else {
                console.error('Failed to save note');
            }
        });

        async function deleteNote(id) {
            const response = await fetch(`/notes/${id}`, { method: 'DELETE' });
            if (response.ok) {
                fetchNotes();
            } else {
                console.error('Failed to delete note');
            }
        }

        window.onload = fetchNotes;
    </script>
</body>
</html>


