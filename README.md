#Mini Project
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct txn
{
    char type[20];
    int amount;
};

struct user
{
    int AccountId;
    char name[100];
    int balance;
    struct txn transactions[100];
    int txnCounter;
};

struct user dataStore[100];
int dataStoreCounter = 0;

void createAccount(char *name)
{
    struct user newUser;

    strcpy(newUser.name, name);
    newUser.balance = 0;
    newUser.AccountId = rand();
    newUser.txnCounter = 0;

    dataStore[dataStoreCounter] = newUser;
    dataStoreCounter++;

    printf("Your Account has been created successfully for %s.\n", newUser.name);
    printf("Your new account ID is: ");
    printf("%d\n", newUser.AccountId);
}

int searchUser(int AccountId)
{
    int index = -1;
    for (int i = 0; i < dataStoreCounter; i++)
    {
        if (dataStore[i].AccountId == AccountId)
        {
            index = i;
            break;
        }
    }

    if (index == -1)
    {
        printf("No user found.\n");
    }

    return index;
}

void viewPassbook(int AccountId)
{
    int index = searchUser(AccountId);

    if (index == -1)
        return;

    printf("S.No.     Type         Amount           Balance\n");
    int bal = 0;
    for (int i = 0; i < dataStore[index].txnCounter; i++)
    {
        if (strcmp(dataStore[index].transactions[i].type, "debit ") == 0)
            bal = bal - dataStore[index].transactions[i].amount;
        else
            bal = bal + dataStore[index].transactions[i].amount;
        printf("%d        %s          %d             %d", i + 1, dataStore[index].transactions[i].type, dataStore[index].transactions[i].amount, bal);
        printf("\n");
    }
}

void depositAmount(int AccountId, int amount)
{
    int index = searchUser(AccountId);

    if (index == -1)
        return;

    struct user currentUser = dataStore[index];

    struct txn transaction;
    strcpy(transaction.type, "credit");
    transaction.amount = amount;

    currentUser.transactions[currentUser.txnCounter] = transaction;
    currentUser.txnCounter++;

    currentUser.balance = (currentUser.balance) + amount;

    dataStore[index] = currentUser;

    printf("Amount deposited successfully.\nUpdated balance is Rs.%d/-\n", currentUser.balance);
}

void withdrawAmount(int AccountId, int amount)
{
    int index = searchUser(AccountId);

    if (index == -1)
        return;

    struct user currentUser = dataStore[index];

    if (((currentUser.balance) - amount) < 0)
    {
        printf("ERROR! Overdrawing not allowed. Cannot withdraw amount.\n");
        return;
    }

    struct txn transaction;
    strcpy(transaction.type, "debit ");
    transaction.amount = amount;

    currentUser.transactions[currentUser.txnCounter] = transaction;
    currentUser.txnCounter++;

    currentUser.balance = (currentUser.balance) - amount;

    dataStore[index] = currentUser;

    printf("Amount withdrawn successfully.\nUpdated balance is Rs.%d/-\n", currentUser.balance);
}

int main()
{
    int loop = 1;
    while (loop)
    {
        printf("Welcome to ABC Bank!\n=============================\n\nPlease choose an option to continue:\n");
        printf("1. Create a new savings account\n");
        printf("2. View passbook for an account\n");
        printf("3. Deposit money to an account\n");
        printf("4. Withdraw money from an account\n");
        printf("Any other key to quit\n\n");

        printf("Please enter your choice: ");
        char choice;
        scanf(" %c", &choice);
        printf("\n");

        int AccountId, amount;
        switch (choice)
        {
        case '1':
            if (dataStoreCounter > 99)
            {
                printf("ERROR! Out of space. Cannot create a new user.\n");
                loop = 0;
                break;
            }
            printf("Enter your name: ");
            char name[100];
            scanf("%s", name);
            createAccount(name);
            break;
        case '2':
            printf("Enter account ID: ");
            scanf("%d", &AccountId);
            viewPassbook(AccountId);
            break;
        case '3':
            printf("Enter account ID: ");
            scanf("%d", &AccountId);
            printf("Enter amount to be deposited: Rs.");
            scanf("%d", &amount);
            depositAmount(AccountId, amount);
            break;
        case '4':
            printf("Enter account ID: ");
            scanf("%d", &AccountId);
            printf("Enter amount to be withdrawn: Rs.");
            scanf("%d", &amount);
            withdrawAmount(AccountId, amount);
            break;
        default:
            printf("Thank you for using ABC Bank!");
            loop = 0;
        }

        printf("\n");
    }

    return 0;
}
