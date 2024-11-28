# **AMKT V2: Comprehensive Developer Setup Guide**

Alongside Crypto Market Index (AMKT) is a fully backed market index, providing exposure to a market-cap weighted basket of assets, to be rebalanced quarterly.

The next iteration of AMKT moves custody on-chain, relying on Vault to custody underlying assets, and on governance to submit accurate Bounty for the next set of underlying assets to rebalance.

---

This guide provides an all-inclusive setup for **AMKT V2** development across **Windows**, **Linux**, and **macOS** platforms. It covers prerequisites, installations, and environment configurations for blockchain development, front-end, back-end, and testing.

## **Prerequisites**

### **General Requirements**

Before proceeding, ensure the following tools and dependencies are installed:

#### **Tools**

1. **Git (Version Control)**
    - **Install:** [Git Downloads](https://git-scm.com/downloads)
    - **Verify:**

bash

    git --version

1. **Visual Studio Code (VS Code) (Code Editor)**
    - **Install:** [VS Code Downloads](https://code.visualstudio.com/)
    - **Recommended Extensions:**
        - Prettier - Code Formatter
        - Solidity
        - Python
        - Docker
2. **Node.js** (For JavaScript/TypeScript development)
    - **Recommended Version:** 16.x or higher
    - **Install:** [Node.js Downloads](https://nodejs.org/)
    - **Verify:**

bash

    node -v
---
    npm -v

3. **Python** (For backend scripting and analytics)
    - **Recommended Version:** 3.8 or higher
    - **Install:** [Python Downloads](https://www.python.org/downloads/)
    - **Verify:**

bash

    python3 --version
---
    pip --version

4. **Docker** (Optional, for containerized development)
    - **Install:** Docker Downloads
    - **Verify:**

bash

    docker --version

5. **TeX** (For documentation contributions)
    - **Install:** TeX Distributions

**Blockchain-Specific Tools**

6. **Solidity Compiler**
    - Installed as part of Hardhat dependencies.
7. **Hardhat (Ethereum Development Framework)**
    - Install:

bash

    npm install --save-dev hardhat

1. **Ganache** (Optional, for local Ethereum testing)
    - Install:

bash

    npm install -g ganache

8. **MetaMask Wallet**
    - Set up a MetaMask wallet for testing deployments.
---
<br><br>
## **Environment-Specific Setup**

### **Linux**

1. **Install Prerequisites**

bash

    sudo apt update
---
    sudo apt install git python3 python3-pip build-essential curl
---
    curl -fsSL <https://deb.nodesource.com/setup_16.x> | sudo -E bash -
---
    sudo apt install -y nodejs

 **Verifying Installations**

bash

    git --version # Verify Git
---
    node -v # Verify Node.js
---
    npm -v # Verify NPM
---
    python3 --version # Verify Python
---
    pip --version # Verify Pip

2. **Installing VS Code**

bash

    sudo snap install --classic code
---
### **Windows**

1. **Install Prerequisites**
    - Download and install:
        - [Git for Windows](https://git-scm.com/download/win). Use Git Bash for terminal operations.
        - [Python](https://www.python.org/downloads/). During installation, check **"Add Python to PATH."**
        - [Node.js](https://nodejs.org/).
        - _(Optional)_ Docker Desktop.
2. **Install VS Code**
    - Download and install from [VS Code Downloads](https://code.visualstudio.com/).
3. **Verify Installations**  
    Open Git Bash or PowerShell and run:

bash

    git --version # Verify Git
---
    node -v # Verify Node.js
---
    npm -v # Verify NPM
---
    python --version # Verify Python

### **macOS**

1. **Install Prerequisites**
    - Install **Homebrew** (Package Manager):

bash

    /bin/bash -c "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh>)"

- - Install dependencies:

bash

    brew install git python3 node
---
    brew install --cask docker

1.1. **Verifying Installations**

bash

    git --version # Verify Git
---
    node -v # Verify Node.js
----
    npm -v # Verify NPM
---
    python3 --version # Verify Python
---
    pip --version # Verify Pip

2. **Installing VS Code**

bash

    brew install --cask visual-studio-code
<br><br>

## **AMK V2 Development Setup**

### 1. **Clone the Repository**

bash

    git clone <https://github.com/Alongside-Finance/amkt-v2.git>
---
    cd amk-v2

2. **Install Dependencies**

bash
#### For JavaScript dependencies

    npm install 
---
#### For Python dependencies
    pip install -r requirements.txt 
<br><br>
## 1. **Environment Configuration**
    - Create a .env file based on the .env.example file in the repository.
    - Configure blockchain nodes, private keys, and other environment variables.
---
### 2. **Hardhat Setup**

bash
### Compile Solidity contracts
    npx hardhat compile 
### Run contract tests
    npx hardhat test 

**Optional: Containerized Setup**

1. **Using Docker Compose**  
    Define a docker-compose.yml for Node.js, Python, and Ethereum services.

yaml

version: '3.8'

services:

app:

build: .

ports:

\- "3000:3000"

volumes:

\- .:/usr/src/app

1. **Run Docker Services**

bash

    docker-compose up

<br><br>
## **Git Installation**

1. ### **macOS**:
    - Install using Homebrew:
  <br><br>

bash

    brew install git

- - Use Xcode Command Line Tools:
<br><br>

bash

    xcode-select --install
<br><br>

1. ### **Linux**:
    - Use the package manager:

bash

    sudo apt update

bash

    sudo apt install git
<br><br>

### **Verifying Installation**

bash

    git --version

### **Configuring Git**

1. Set your username:

bash

    git config --global user.name "Your Name"

2. Set your email:

bash

    git config --global user.email "<your.email@example.com>"

3. Configure line endings:
   <br><br>

    - ### **Windows**:

bash

    git config --global core.autocrlf true

<br><br>

- - ### **Linux/macOS**:
  <br><br>

bash

    git config --global core.autocrlf input

<br><br>

- - ### **Disable line-ending conversion** (if not needed):

bash

        git config --global core.autocrlf false

 <br><br>   
----



# Cloning the Repository


###  **Clone the Repository Locally**


Open your terminal or command prompt and run the following command:

bash

    git clone https://github.com/Threshold-USD/dev.git

---
<br><br>

## Installing Required Languages and Tools

This guide provides step-by-step instructions for installing JavaScript, TypeScript, Python, TeX, and Solidity across Linux, Windows, and macOS. Follow the instructions relevant to your operating system.

## Prerequisites

Before starting, ensure you have the following installed:

- A terminal or command-line tool (Bash, Git Bash, or Terminal)
- Administrative privileges on your machine

## JavaScript (Node.js and npm)

### Linux

1. ### Update your system:

bash

    sudo apt update

2. ### Install Node.js and npm:

bash

curl -fsSL <https://deb.nodesource.com/setup_16.x> | sudo -E bash -

sudo apt install -y nodejs

2. ### Verify installation:

bash

    node -v
---
    npm -v
<br><br>

### Windows

1. Download the Node.js installer from [Node.js Downloads](https://nodejs.org/).
2. Run the installer and follow the prompts.
3. Verify installation using Git Bash or PowerShell:

bash

    node -v
---
    npm -v
<br><br>

### macOS

1. ### Install Node.js and npm using Homebrew:

bash

    brew install node

2. ### Verify installation:

bash

    node -v
---
    npm -v
---
<br><br>

## TypeScript

### All Operating Systems

1. ### Install TypeScript globally using npm:

bash

    npm install -g typescript

2. ### Verify installation:

bash

    tsc --version
---
<br><br>

## Python

### Linux

1. ### Install Python:

bash

    sudo apt update
---

    sudo apt install python3 python3-pip

2. ### Verify installation:

bash

    python3 --version
---

    pip3 --version

---
<br><br>

### Windows

1. Download the Python installer from [Python.org](https://www.python.org/).
2. Run the installer and check "Add Python to PATH" during setup.
3. Verify installation using Git Bash or PowerShell:

bash

    python --version
---
    pip --version

### macOS

1. ### Install Python using Homebrew:

bash

    brew install python

2. ### Verify installation:

bash

    python3 --version
---

    pip3 --version
<br><br>
# TeX

## Linux

1. ###  Install a TeX distribution:

bash

    sudo apt update
---

    sudo apt install texlive-full

2. ### Verify installation:

bash

    pdflatex --version
<br><br>

## Windows

1. Download and install [MiKTeX](https://miktex.org/).
2. Verify installation using the MiKTeX console.
   
   ---
<br><br>

### macOS

### 1. Install TeX using Homebrew:

bash

    brew install --cask mactex
---


 ### 2.  Verify installation:

bash

    pdflatex --version
---
<br><br>

## Solidity

### All Operating Systems

1. ### Install Solidity globally using npm:

bash

    npm install -g solc

2. ###  Verify installation:

bash

    solc --version
---
<br><br>
## **Language Setup: Rust**

This section explains how to set up Rust on Windows, macOS, and Linux, as well as how to install project dependencies efficiently.

### **Step 1: Installing Rust**

#### **For Windows**

1. Download the Rust installer from the [Rust Downloads page](https://www.rust-lang.org/tools/install).
2. Run the installer and follow the prompts. Ensure the **"Add Rust to PATH"** option is selected.
3. Once installed, open PowerShell or Command Prompt and verify the installation:

bash

    rustc --version
----
    cargo --version

 #### **For macOS**

1. Install **Homebrew** (if not already installed):

bash

    /bin/bash -c "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh>)"

2. Install Rust via Homebrew:

bash

    brew install rustup
---
    rustup-init
---
3. Add Rust to your shell configuration:

bash

    source $HOME/.cargo/env

4. Verify the installation:

bash

    rustc --version
---
    cargo --version

**For Linux**

1. Open a terminal and use the rustup installation script:

bash

    curl --proto '=https' --tlsv1.2 -sSf <https://sh.rustup.rs> | sh

 Follow the on-screen prompts to complete the installation.

2. Add Rust to your shell environment:

bash

    source $HOME/.cargo/env

3. Verify the installation:

bash

    rustc --version
---
    cargo --version

### **Step 2: Updating Rust**

After installation, keep Rust up to date by running:

bash

    rustup update

**Step 3: Create and Run a Rust Project**

1. Create a new project:

bash

    cargo new my_project

cd my_project

1. Open the src/main.rs file to edit your Rust code.
2. Build and run the project:

bash

cargo run

### **Step 4: Installing Project Dependencies**

Rust uses a Cargo.toml file to manage dependencies. Follow these steps to add and install them:

1. Open the [Open Cargo.toml](./sdk/Cargo.toml)
 file in your project directory.

###  Install the dependencies by running:

bash

    cargo build

Cargo will download and compile the dependencies specified in Cargo.toml.

#### To check if dependencies are installed and up to date:

bash

    cargo check
<br><br>

### **Step 5: Testing and Formatting**

1. **Run Tests**:

bash

    cargo test

 **Format Code**: Install Rustfmt if not already included:

bash

    rustup component add rustfmt

Format your code:

bash

    cargo fmt

1. **Lint Code**: Install Clippy for linting:

bash

    rustup component add clippy
---
    cargo clippy

<br><br>