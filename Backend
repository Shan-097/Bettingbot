from flask import Flask, render_template, jsonify, request
import sqlite3

app = Flask(__name__)

# Initialize database (use SQLite for simplicity)
def init_db():
    conn = sqlite3.connect('bets.db')
    c = conn.cursor()
    c.execute('''
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            username TEXT,
            balance INTEGER
        )
    ''')
    c.execute('''
        CREATE TABLE IF NOT EXISTS bets (
            id INTEGER PRIMARY KEY,
            user_id INTEGER,
            match_id INTEGER,
            team TEXT,
            amount INTEGER
        )
    ''')
    conn.commit()
    conn.close()

@app.route('/')
def index():
    # Return the basic HTML page
    return render_template('index.html')

@app.route('/matches', methods=['GET'])
def get_matches():
    # Example matches data (can be dynamic)
    matches = [
        {"id": 1, "teams": "Team A vs Team B", "odds": {"teamA": 2.5, "teamB": 3.0}},
        {"id": 2, "teams": "Team C vs Team D", "odds": {"teamC": 1.8, "teamD": 2.2}},
    ]
    return jsonify(matches)

@app.route('/place_bet', methods=['POST'])
def place_bet():
    data = request.get_json()
    user_id = data['user_id']
    match_id = data['match_id']
    team = data['team']
    amount = data['amount']

    conn = sqlite3.connect('bets.db')
    c = conn.cursor()

    # Check balance
    c.execute('SELECT balance FROM users WHERE id = ?', (user_id,))
    balance = c.fetchone()
    if not balance or balance[0] < amount:
        return jsonify({"message": "Insufficient balance"}), 400

    # Update balance
    new_balance = balance[0] - amount
    c.execute('UPDATE users SET balance = ? WHERE id = ?', (new_balance, user_id))

    # Place the bet
    c.execute('INSERT INTO bets (user_id, match_id, team, amount) VALUES (?, ?, ?, ?)',
              (user_id, match_id, team, amount))

    conn.commit()
    conn.close()
    return jsonify({"message": "Bet placed successfully", "new_balance": new_balance})

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
