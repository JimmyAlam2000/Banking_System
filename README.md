# 🏦 Banking System

> A console-based Banking Management System built in **C++** with persistent file storage, full CRUD operations, and real-time balance management.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Compilation](#compilation)
  - [Running the Program](#running-the-program)
- [Usage Guide](#usage-guide)
- [Architecture](#architecture)
  - [Class: Account](#class-account)
  - [Class: Bank](#class-bank)
  - [Exception: InsufficientFunds](#exception-insufficientfunds)
- [Data Storage](#data-storage)
- [Known Limitations](#known-limitations)
- [Changelog](#changelog)
- [License](#license)

---

## Overview

The **Banking System** is a terminal-based C++ application that simulates core banking operations. It allows users to create accounts, deposit and withdraw funds, check balances, and close accounts — all from an interactive menu. Account data is automatically persisted to a local file so records are retained between sessions.

This project demonstrates key C++ concepts including:
- Object-Oriented Programming (classes, encapsulation)
- File I/O with `fstream`
- Operator overloading
- STL containers (`std::map`)
- Exception handling

---

## Features

| Feature | Description |
|---|---|
| 🆕 Open Account | Create a new account with auto-generated unique account number |
| 🔍 Balance Enquiry | View full account details and current balance |
| ➕ Deposit | Add funds to any existing account |
| ➖ Withdraw | Withdraw funds with minimum balance enforcement |
| ❌ Close Account | Permanently delete an account from the system |
| 📋 Show All Accounts | List every account with details |
| 💾 Persistent Storage | All data saved to `Bank.data` and restored on next run |
| 🔤 Full Name Support | First and last names support spaces (e.g. `Mary Jane`) |
| 💰 Formatted Balance | Balance displayed in clean decimal format (e.g. `8000000.00`) |

---

## Tech Stack

- **Language:** C++17
- **Compiler:** G++ (MinGW / MSYS2 / GCC)
- **Libraries:** STL (`iostream`, `fstream`, `map`, `vector`, `iomanip`)
- **Storage:** Plain-text flat file (`Bank.data`)
- **Platform:** Windows / Linux / macOS

---

## Project Structure

```
Banking_System/
│
├── Banking_System.cpp    # Complete source code (single-file project)
└── Bank.data             # Auto-generated persistent data file (do not edit manually)
```

> `Bank.data` is created automatically on first run. Do not manually edit it — incorrect formatting will cause data to load incorrectly.

---

## Getting Started

### Prerequisites

You need a C++ compiler installed:

| Platform | Recommended Compiler |
|---|---|
| Windows | [MSYS2 + MinGW](https://www.msys2.org/) or [TDM-GCC](https://jmeubank.github.io/tdm-gcc/) |
| Linux | GCC via `sudo apt install g++` |
| macOS | Clang via `xcode-select --install` |

Verify your compiler is working:
```bash
g++ --version
```

---

### Compilation

Open a terminal in the project folder and run:

```bash
g++ Banking_System.cpp -o Banking_System
```

For a cleaner build with warnings enabled (recommended):
```bash
g++ -Wall -std=c++17 Banking_System.cpp -o Banking_System
```

---

### Running the Program

**Windows:**
```bash
.\Banking_System.exe
```

**Linux / macOS:**
```bash
./Banking_System
```

> ⚠️ **Windows users:** Always close the running `.exe` window before recompiling. Windows locks executables while they are running, which will cause a `Permission denied` linker error if you try to rebuild without closing first.

---

## Usage Guide

Once launched, you will see the main menu:

```
****Banking System****

    Select one option below
    1 Open an Account
    2 Balance Enquiry
    3 Deposit
    4 Withdrawal
    5 Close an Account
    6 Show All Accounts
    7 Quit

Enter your choice:
```

### Opening an Account
Select `1`, then enter:
- First Name (spaces allowed, e.g. `Mary Jane`)
- Last Name (spaces allowed, e.g. `Van Dyke`)
- Initial Balance (must be at least `500` to satisfy minimum balance rule)

```
Enter First Name: John Michael
Enter Last Name: Smith
Enter initial Balance: 5000

Congratulation Account is Created
First Name: John Michael
Last Name: Smith
Account Number: 1
Balance: 5000.00
```

### Depositing Funds
Select `3`, enter the account number and the amount to deposit.

### Withdrawing Funds
Select `4`, enter the account number and the amount to withdraw.

> ⚠️ A minimum balance of **500** must be maintained at all times. Attempting to withdraw below this threshold will show an error instead of crashing.

```
Error: Insufficient Funds! Minimum balance of 500 must be maintained.
```

### Closing an Account
Select `5` and enter the account number. The account is permanently removed from the system.

---

## Architecture

### Class: `Account`

Represents a single bank account.

**Private Members:**

| Member | Type | Description |
|---|---|---|
| `accountNumber` | `long` | Unique auto-incremented identifier |
| `firstName` | `string` | Account holder's first name |
| `lastName` | `string` | Account holder's last name |
| `balance` | `float` | Current account balance |
| `NextAccountNumber` | `static long` | Shared counter for auto-incrementing IDs |

**Public Methods:**

| Method | Description |
|---|---|
| `Account(fname, lname, balance)` | Parameterized constructor — assigns next available account number |
| `getAccNo()` | Returns the account number |
| `getFirstName()` / `getLastName()` | Returns name fields |
| `getBalance()` | Returns current balance |
| `Deposit(amount)` | Adds amount to balance |
| `Withdraw(amount)` | Deducts amount; throws `InsufficientFunds` if minimum balance would be breached |
| `setLastAccountNumber(n)` | Static — restores counter when loading from file |
| `getLastAccountNumber()` | Static — returns current counter value |

**Overloaded Operators:**

| Operator | Streams | Purpose |
|---|---|---|
| `operator<<` | `ofstream` | Serializes account data to file |
| `operator>>` | `ifstream` | Deserializes account data from file |
| `operator<<` | `ostream` | Pretty-prints account details to console |

---

### Class: `Bank`

Manages all accounts using a `std::map<long, Account>` keyed by account number.

**Methods:**

| Method | Description |
|---|---|
| `Bank()` | Constructor — loads all accounts from `Bank.data` on startup |
| `OpenAccount(fname, lname, balance)` | Creates account, inserts into map, writes to file |
| `BalanceEnquiry(accountNumber)` | Finds and returns account by number |
| `Deposit(accountNumber, amount)` | Finds account and calls `Account::Deposit()` |
| `Withdraw(accountNumber, amount)` | Finds account and calls `Account::Withdraw()` |
| `CloseAccount(accountNumber)` | Removes account from map |
| `ShowAllAccounts()` | Iterates and prints all accounts |
| `~Bank()` | Destructor — writes all accounts back to `Bank.data` on exit |

---

### Exception: `InsufficientFunds`

A lightweight custom exception class thrown by `Account::Withdraw()` when the requested withdrawal would reduce the balance below `MIN_BALANCE`.

```cpp
class InsufficientFunds{};
```

Caught in `main()` inside `case 4` with a user-friendly error message.

---

## Data Storage

All account data is stored in **`Bank.data`**, a plain-text file in the same directory as the executable.

**File format (per account):**
```
<accountNumber>
<firstName>
<lastName>
<balance>
```

**Example `Bank.data`:**
```
1
John Michael
Smith
5000
2
Mary Jane
Watson
12000
```

The file is:
- **Read** at startup by `Bank::Bank()` to restore all previous accounts
- **Written** immediately when a new account is opened via `OpenAccount()`
- **Written** on program exit by the `Bank` destructor `~Bank()`

---

## Known Limitations

| Limitation | Details |
|---|---|
| No account number validation | Entering a non-existent account number causes undefined behavior |
| `float` precision | `float` is used for balance; for production, use `double` or integer cents |
| No authentication | No PIN or password system — any user can access any account |
| Single-user | No multi-user or concurrent access support |
| `while(!eof())` loop | Can insert last record twice on some systems — safer to use `while(file >> obj)` |

---

## Changelog

### v2.0 (Current)
- ✅ Fixed: Names with spaces now work correctly (`getline` replaces `cin >>`)
- ✅ Fixed: `operator>>` updated to use `getline` so spaced names load correctly from file
- ✅ Fixed: Missing `break` in `case 5` that caused fall-through into `ShowAllAccounts`
- ✅ Fixed: `InsufficientFunds` exception now caught in `main()` — no more crash on bad withdrawal
- ✅ Fixed: Balance now displays in decimal format (`8000000.00`) instead of scientific notation (`8e+06`)
- ✅ Fixed: `cin.ignore()` added throughout to prevent ghost input bugs
- ✅ Fixed: `ShowAllAccounts` label corrected from `"Amount"` to `"Account"`

### v1.0
- Initial release with basic banking operations and file persistence

---

<div align="center">
  <sub>Built with C++ • Console Application • File-based Persistence</sub>
</div>
