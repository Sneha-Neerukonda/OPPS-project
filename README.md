# OPPS-code
sneha Neerukonda 
mokshitha
tejaswi 
Anitha


#include <iostream>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// Base class for Account
class Account {
protected:
    string accountNumber;
    string accountHolderName;
    double balance;

public:
    Account(string accNum, string holderName, double bal = 0.0) 
        : accountNumber(accNum), accountHolderName(holderName), balance(bal) {}

    virtual ~Account() = default;

    virtual void deposit(double amount) {
        balance += amount;
        cout << "Deposited: $" << amount << endl;
    }

    virtual void withdraw(double amount) {
        if (amount > balance) {
            cout << "Insufficient balance!" << endl;
        } else {
            balance -= amount;
            cout << "Withdrawn: $" << amount << endl;
        }
    }

    virtual void display() const {
        cout << "Account Number: " << accountNumber << endl;
        cout << "Account Holder: " << accountHolderName << endl;
        cout << "Balance: $" << balance << endl;
    }

    string getAccountNumber() const { return accountNumber; }
    string getAccountHolderName() const { return accountHolderName; }
    double getBalance() const { return balance; }
    void setAccountHolderName(const string &name) { accountHolderName = name; }
};

// Derived class for Savings Account
class SavingsAccount : public Account {
private:
    double interestRate;

public:
    SavingsAccount(string accNum, string holderName, double rate, double bal = 0.0) 
        : Account(accNum, holderName, bal), interestRate(rate) {}

    void addInterest() {
        balance += (balance * interestRate / 100);
    }

    void display() const override {
        cout << "Savings ";
        Account::display();
        cout << "Interest Rate: " << interestRate << "%" << endl;
    }
};

// Derived class for Current Account
class CurrentAccount : public Account {
private:
    double overdraftLimit;

public:
    CurrentAccount(string accNum, string holderName, double limit, double bal = 0.0) 
        : Account(accNum, holderName, bal), overdraftLimit(limit) {}

    void withdraw(double amount) override {
        if (amount > balance + overdraftLimit) {
            cout << "Overdraft limit exceeded!" << endl;
        } else {
            balance -= amount;
            cout << "Withdrawn: $" << amount << endl;
        }
    }

    void display() const override {
        cout << "Current ";
        Account::display();
        cout << "Overdraft Limit: $" << overdraftLimit << endl;
    }
};

// Bank Management System
class Bank {
private:
    vector<unique_ptr<Account>> accounts;

public:
    void createAccount() {
        string accNum, holderName;
        double balance;
        char accountType;
        cout << "Enter Account Number: ";
        cin >> accNum;
        cout << "Enter Account Holder Name: ";
        cin.ignore();
        getline(cin, holderName);
        cout << "Enter Initial Balance: ";
        cin >> balance;
        cout << "Enter Account Type (S for Savings / C for Current): ";
        cin >> accountType;

        if (accountType == 'S' || accountType == 's') {
            double interestRate;
            cout << "Enter Interest Rate: ";
            cin >> interestRate;
            accounts.push_back(make_unique<SavingsAccount>(accNum, holderName, interestRate, balance));
        } else if (accountType == 'C' || accountType == 'c') {
            double overdraftLimit;
            cout << "Enter Overdraft Limit: ";
            cin >> overdraftLimit;
            accounts.push_back(make_unique<CurrentAccount>(accNum, holderName, overdraftLimit, balance));
        }
        cout << "Account Created Successfully!" << endl;
    }

    void depositAmount() {
        string accNum;
        double amount;
        cout << "Enter Account Number: ";
        cin >> accNum;
        cout << "Enter Amount to Deposit: ";
        cin >> amount;

        for (const auto& account : accounts) {
            if (account->getAccountNumber() == accNum) {
                account->deposit(amount);
                return;
            }
        }
        cout << "Account not found!" << endl;
    }

    void withdrawAmount() {
        string accNum;
        double amount;
        cout << "Enter Account Number: ";
        cin >> accNum;
        cout << "Enter Amount to Withdraw: ";
        cin >> amount;

        for (const auto& account : accounts) {
            if (account->getAccountNumber() == accNum) {
                account->withdraw(amount);
                return;
            }
        }
        cout << "Account not found!" << endl;
    }

    void balanceEnquiry() const {
        string accNum;
        cout << "Enter Account Number: ";
        cin >> accNum;

        for (const auto& account : accounts) {
            if (account->getAccountNumber() == accNum) {
                cout << "Current Balance: $" << account->getBalance() << endl;
                return;
            }
        }
        cout << "Account not found!" << endl;
    }

    void listAllAccounts() const {
        for (const auto& account : accounts) {
            account->display();
            cout << "---------------------------" << endl;
        }
    }

    void closeAccount() {
        string accNum;
        cout << "Enter Account Number to Close: ";
        cin >> accNum;

        for (auto it = accounts.begin(); it != accounts.end(); ++it) {
            if ((*it)->getAccountNumber() == accNum) {
                accounts.erase(it);
                cout << "Account closed successfully!" << endl;
                return;
            }
        }
        cout << "Account not found!" << endl;
    }

    void modifyAccount() {
        string accNum;
        cout << "Enter Account Number to Modify: ";
        cin >> accNum;

        for (const auto& account : accounts) {
            if (account->getAccountNumber() == accNum) {
                string newName;
                cout << "Enter New Account Holder Name: ";
                cin.ignore();
                getline(cin, newName);
                account->setAccountHolderName(newName);
                cout << "Account details updated successfully!" << endl;
                return;
            }
        }
        cout << "Account not found!" << endl;
    }
};

// Main function to interact with the Bank Management System
int main() {
    Bank myBank;
    int choice;

    while (true) {
        cout << "\n--- Bank Management System ---\n";
        cout << "1. Create New Account\n";
        cout << "2. Deposit Amount\n";
        cout << "3. Withdraw Amount\n";
        cout << "4. Balance Enquiry\n";
        cout << "5. List of All Account Holders\n";
        cout << "6. Close an Account\n";
        cout << "7. Modify an Account\n";
        cout << "8. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: myBank.createAccount(); break;
            case 2: myBank.depositAmount(); break;
            case 3: myBank.withdrawAmount(); break;
            case 4: myBank.balanceEnquiry(); break;
            case 5: myBank.listAllAccounts(); break;
            case 6: myBank.closeAccount(); break;
            case 7: myBank.modifyAccount(); break;
            case 8: cout << "Exiting system. Goodbye!\n"; return 0;
            default: cout << "Invalid choice! Try again.\n";
        }
    }
}
