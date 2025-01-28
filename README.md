# Bank-Management
Bank Management System in C. It will include features like account creation, deposit, withdrawal, balance inquiry, and account deletion.
Here’s a basic blueprint for creating a Bank Management System in C. It will include features like account creation, deposit, withdrawal, balance inquiry, and account deletion.

Features
Create Account: Add new customer account information.
Deposit Money: Add money to an existing account.
Withdraw Money: Deduct money from an account (with balance check).
Check Balance: View the current balance of an account.
Delete Account: Remove a customer account.
Exit: Exit the application.
Implementation Outline
The project will use file handling for data persistence, so accounts and their data remain stored even after the program ends.

Code
Here is a complete implementation:

c
Copy
Edit
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    int accountNumber;
    char name[100];
    float balance;
} Account;

void createAccount();
void depositMoney();
void withdrawMoney();
void checkBalance();
void deleteAccount();
void displayMenu();

int main() {
    int choice;
    while (1) {
        displayMenu();
        printf("\nEnter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                createAccount();
                break;
            case 2:
                depositMoney();
                break;
            case 3:
                withdrawMoney();
                break;
            case 4:
                checkBalance();
                break;
            case 5:
                deleteAccount();
                break;
            case 6:
                printf("\nThank you for using the Bank Management System. Goodbye!\n");
                exit(0);
            default:
                printf("\nInvalid choice! Please try again.\n");
        }
    }
    return 0;
}

void displayMenu() {
    printf("\n=== Bank Management System ===\n");
    printf("1. Create Account\n");
    printf("2. Deposit Money\n");
    printf("3. Withdraw Money\n");
    printf("4. Check Balance\n");
    printf("5. Delete Account\n");
    printf("6. Exit\n");
}

void createAccount() {
    Account account;
    FILE *file = fopen("accounts.dat", "ab");

    if (!file) {
        printf("\nError: Unable to open file.\n");
        return;
    }

    printf("\nEnter Account Number: ");
    scanf("%d", &account.accountNumber);
    printf("Enter Name: ");
    getchar(); // To consume leftover newline
    fgets(account.name, 100, stdin);
    account.name[strcspn(account.name, "\n")] = '\0'; // Remove trailing newline
    account.balance = 0;

    fwrite(&account, sizeof(Account), 1, file);
    fclose(file);

    printf("\nAccount created successfully!\n");
}

void depositMoney() {
    int accountNumber;
    float amount;
    Account account;
    FILE *file = fopen("accounts.dat", "rb+");

    if (!file) {
        printf("\nError: Unable to open file.\n");
        return;
    }

    printf("\nEnter Account Number: ");
    scanf("%d", &accountNumber);

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.accountNumber == accountNumber) {
            printf("Enter Amount to Deposit: ");
            scanf("%f", &amount);

            account.balance += amount;
            fseek(file, -sizeof(Account), SEEK_CUR);
            fwrite(&account, sizeof(Account), 1, file);

            printf("\nDeposit successful! New Balance: %.2f\n", account.balance);
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("\nAccount not found.\n");
    }
}

void withdrawMoney() {
    int accountNumber;
    float amount;
    Account account;
    FILE *file = fopen("accounts.dat", "rb+");

    if (!file) {
        printf("\nError: Unable to open file.\n");
        return;
    }

    printf("\nEnter Account Number: ");
    scanf("%d", &accountNumber);

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.accountNumber == accountNumber) {
            printf("Enter Amount to Withdraw: ");
            scanf("%f", &amount);

            if (account.balance >= amount) {
                account.balance -= amount;
                fseek(file, -sizeof(Account), SEEK_CUR);
                fwrite(&account, sizeof(Account), 1, file);

                printf("\nWithdrawal successful! New Balance: %.2f\n", account.balance);
            } else {
                printf("\nInsufficient balance.\n");
            }
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("\nAccount not found.\n");
    }
}

void checkBalance() {
    int accountNumber;
    Account account;
    FILE *file = fopen("accounts.dat", "rb");

    if (!file) {
        printf("\nError: Unable to open file.\n");
        return;
    }

    printf("\nEnter Account Number: ");
    scanf("%d", &accountNumber);

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.accountNumber == accountNumber) {
            printf("\nAccount Number: %d\nName: %s\nBalance: %.2f\n", account.accountNumber, account.name, account.balance);
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("\nAccount not found.\n");
    }
}

void deleteAccount() {
    int accountNumber;
    Account account;
    FILE *file = fopen("accounts.dat", "rb");
    FILE *tempFile = fopen("temp.dat", "wb");

    if (!file || !tempFile) {
        printf("\nError: Unable to open file.\n");
        return;
    }

    printf("\nEnter Account Number to Delete: ");
    scanf("%d", &accountNumber);

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.accountNumber == accountNumber) {
            found = 1;
            printf("\nAccount deleted successfully.\n");
        } else {
            fwrite(&account, sizeof(Account), 1, tempFile);
        }
    }
    fclose(file);
    fclose(tempFile);

    remove("accounts.dat");
    rename("temp.dat", "accounts.dat");

    if (!found) {
        printf("\nAccount not found.\n");
    }
}
Steps to Run
Save the code to a file, e.g., bank_management.c.
Compile it using GCC:
bash
Copy
Edit
gcc bank_management.c -o bank_management
Run the executable:
bash
Copy
Edit
./bank_management
Enhancements
Add password protection for accounts.
Include transaction history.
Improve the interface with colored output using libraries like conio.h (Windows) or ANSI escape codes (Linux).
