1. **Issue Picked:** Pulls the highest priority ticket off GitHub.
2. **Claude Code Unleashed:** Claude reads the repo, writes code, runs terminal commands, and fixes errors.
3. **Ambiguity Gate:** If the spec is unclear, it pings you. Otherwise, it pushes to `main` and closes the ticket.

---

## ✨ Features

* **🎫 GitHub Ticketing Sync:** Auto-reads issues, assigns tasks, and updates tags so you look busy in standups.
* **⚡ Claude Code Drive:** Leverages Claude Code as the actual SWE to execute terminal actions and refactor files safely.
* **🛑 Lazy Human Bridge:** Only interruptions allowed are simple multiple-choice questions when your specs make zero sense.
* **🚀 Zero-Bribe Code Reviews:** Pushes directly without demanding equity or free pizza.

---

## 🚀 Quick Start

### Prerequisites
* Node.js 20+
* Anthropic API Key & Claude Code CLI configured
* GitHub Personal Access Token (with repo & issues permissions)

### Setup & Run

```bash
# Clone and enter the void
git clone [https://github.com/your-username/full-stack-unemployed.git](https://github.com/your-username/full-stack-unemployed.git)
cd full-stack-unemployed

# Install dependencies
npm install

# Set up env
cp .env.example .env

# Run the orchestrator
npm start
