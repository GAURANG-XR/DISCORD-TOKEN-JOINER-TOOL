# DISCORD-TOKEN-JOINER-TOOL
A high-performance, multi-threaded tool designed to join Discord servers using account tokens. This tool is built for speed, efficiency, and ease of use.

## ✨ Features

* **Multi-Threaded:** Join servers rapidly using multiple accounts simultaneously.
* **Proxy Support:** Integrated support for HTTP/S, SOCKS4, and SOCKS5 proxies to prevent IP rate-limiting.
* **HCaptcha/HCaptcha Solver Support:** Compatible with popular captcha solving services.
* **Custom Delays:** Set random delay intervals to mimic human behavior and avoid detection.
* **Token Validation:** Automatically filters out dead or locked tokens before starting.

---

## 🛠️ Installation

1. **Clone the Repository**
```bash
git clone https://github.com/yourusername/discord-token-joiner.git
cd discord-token-joiner

```


2. **Install Dependencies**
Ensure you have [Python 3.8+](https://www.python.org/downloads/) installed.
```bash
pip install -r requirements.txt

```


3. **Configuration**
* Add your tokens to `tokens.txt` (one per line).
* Add your proxies to `proxies.txt` (if applicable).
* Edit `config.json` to set your invite code and delay preferences.



---

## 🚀 Usage

Run the main script to start the joiner:

```bash
python main.py

```

### Configuration Options

| Setting | Description |
| --- | --- |
| `invite_code` | The suffix of the Discord link (e.g., `discord.gg/invitecode`) |
| `threads` | Number of simultaneous joins |
| `delay` | Time in seconds between joins |
| `use_proxies` | Set to `true` or `false` |

---

## ⚠️ Disclaimer

> **Notice:** This tool is for educational and research purposes only. Automating Discord accounts (self-botting) is against [Discord's Terms of Service](https://discord.com/terms). Use of this tool may result in the termination of your accounts. The developers assume no liability for any misuse or damage caused by this program.

---


## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://www.google.com/search?q=https://github.com/yourusername/discord-token-joiner/issues).

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

   
Copy this into a file named `requirements.txt`:

```text
requests==2.31.0
colorama==0.4.6
pystyle==2.9

```

---

### 🐍 Token Validator (`validator.py`)

This script uses a fast, multi-threaded approach to check which tokens are still valid before you run the joiner.

```python
import requests
from concurrent.futures import ThreadPoolExecutor
from colorama import Fore, init

init(autoreset=True)

def check_token(token):
    # Discord API endpoint for user data
    url = "https://discord.com/api/v9/users/@me"
    headers = {"Authorization": token}
    
    try:
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            print(f"{Fore.GREEN}[VALID] {token[:25]}...")
            with open("valid_tokens.txt", "a") as f:
                f.write(f"{token}\n")
        elif response.status_code == 401:
            print(f"{Fore.RED}[INVALID] {token[:25]}...")
        elif response.status_code == 403:
            print(f"{Fore.YELLOW}[LOCKED] {token[:25]}...")
        else:
            print(f"{Fore.WHITE}[UNKNOWN] {token[:25]} - {response.status_code}")
    except Exception as e:
        print(f"{Fore.RED}[ERROR] Could not check token: {e}")

def main():
    with open("tokens.txt", "r") as f:
        tokens = [line.strip() for line in f if line.strip()]
    
    print(f"{Fore.CYAN}Checking {len(tokens)} tokens...\n")
    
    # Using 10 threads for speed without hitting rate limits too hard
    with ThreadPoolExecutor(max_workers=10) as executor:
        executor.map(check_token, tokens)

if __name__ == "__main__":
    main()
