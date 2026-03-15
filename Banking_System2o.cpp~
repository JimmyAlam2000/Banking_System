#include<iostream>
#include<fstream>
#include<cstdlib>
#include<vector>
#include<map>
#include<iomanip>
using namespace std;

#define MIN_BALANCE 500
class InsufficientFunds{};

class Account
{
private:
    long accountNumber;
    string firstName;
    string lastName;
    float balance;
    static long NextAccountNumber;
public:
    Account(){}
    Account(string fname,string lname,float balance);
    long getAccNo(){return accountNumber;}
    string getFirstName(){return firstName;}
    string getLastName(){return lastName;}
    float getBalance(){return balance;}

    void Deposit(float amount);
    void Withdraw(float amount);
    static void setLastAccountNumber(long accountNumber);
    static long getLastAccountNumber();
    friend ofstream & operator<<(ofstream &ofs,Account &acc);
    friend ifstream & operator>>(ifstream &ifs,Account &acc);
    friend ostream & operator<<(ostream &os,Account &acc);
};
long Account::NextAccountNumber=0;

class Bank
{
private:
    map<long,Account> accounts;
public:
    Bank();
    Account OpenAccount(string fname,string lname,float balance);
    Account BalanceEnquiry(long accountNumber);
    Account Deposit(long accountNumber,float amount);
    Account Withdraw(long accountNumber,float amount);
    void CloseAccount(long accountNumber);
    void ShowAllAccounts();
    ~Bank();
};

int main()
{
    Bank b;
    Account acc;

    int choice;
    string fname,lname;
    long accountNumber;
    float balance;
    float amount;
    cout<<"****Banking System****"<<endl;
    do
    {
        cout<<"\n\tSelect one option below ";
        cout<<"\n\t1 Open an Account";
        cout<<"\n\t2 Balance Enquiry";
        cout<<"\n\t3 Deposit";
        cout<<"\n\t4 Withdrawal";
        cout<<"\n\t5 Close an Account";
        cout<<"\n\t6 Show All Accounts";
        cout<<"\n\t7 Quit";
        cout<<"\nEnter your choice: ";
        cin>>choice;
        cin.ignore();  // clear leftover '\n' after reading choice

        switch(choice)
        {
            case 1:
                cout<<"Enter First Name: ";
                getline(cin,fname);         // supports spaces in first name
                cout<<"Enter Last Name: ";
                getline(cin,lname);         // supports spaces in last name
                cout<<"Enter initial Balance: ";
                cin>>balance;
                cin.ignore();              // clear '\n' after balance
                acc=b.OpenAccount(fname,lname,balance);
                cout<<endl<<"Congratulation Account is Created"<<endl;
                cout<<acc;
                break;
            case 2:
                cout<<"Enter Account Number: ";
                cin>>accountNumber;
                cin.ignore();
                acc=b.BalanceEnquiry(accountNumber);
                cout<<endl<<"Your Account Details"<<endl;
                cout<<acc;
                break;
            case 3:
                cout<<"Enter Account Number: ";
                cin>>accountNumber;
                cout<<"Enter Amount: ";
                cin>>amount;
                cin.ignore();
                acc=b.Deposit(accountNumber, amount);
                cout<<endl<<"Amount is Deposited"<<endl;
                cout<<acc;
                break;
            case 4:
                cout<<"Enter Account Number: ";
                cin>>accountNumber;
                cout<<"Enter Amount: ";
                cin>>amount;
                cin.ignore();
                try
                {
                    acc=b.Withdraw(accountNumber, amount);
                    cout<<endl<<"Amount Withdrawn"<<endl;
                    cout<<acc;
                }
                catch(InsufficientFunds)
                {
                    cout<<endl<<"Error: Insufficient Funds! Minimum balance of "<<MIN_BALANCE<<" must be maintained."<<endl;
                }
                break;
            case 5:
                cout<<"Enter Account Number: ";
                cin>>accountNumber;
                cin.ignore();
                b.CloseAccount(accountNumber);
                cout<<endl<<"Account is Closed"<<endl;
                break;  // fixed: was missing, caused fall-through into case 6
            case 6:
                b.ShowAllAccounts();
                break;
            case 7: break;
            default:
                    cout<<"\nEnter correct choice ";
                    exit(0);
        }
    }while(choice!=7);
    cin.get();
    return 0;
}

Account::Account(string fname,string lname,float balance)
{
    NextAccountNumber++;
    accountNumber=NextAccountNumber;
    firstName=fname;
    lastName=lname;
    this->balance=balance;
}
void Account::Deposit(float amount)
{
    balance+=amount;
}
void Account::Withdraw(float amount)
{
    if(balance-amount<MIN_BALANCE)
        throw InsufficientFunds();
    balance-=amount;
}
void Account::setLastAccountNumber(long accountNumber)
{
    NextAccountNumber=accountNumber;
}
long Account::getLastAccountNumber()
{
    return NextAccountNumber;
}
ofstream & operator<<(ofstream &ofs,Account &acc)
{
    ofs<<acc.accountNumber<<endl;
    ofs<<acc.firstName<<endl;
    ofs<<acc.lastName<<endl;
    ofs<<acc.balance<<endl;
    return ofs;
}
ifstream & operator>>(ifstream &ifs,Account &acc)
{
    ifs>>acc.accountNumber;
    ifs.ignore();                   // skip '\n' after account number
    getline(ifs,acc.firstName);     // supports spaces in first name
    getline(ifs,acc.lastName);      // supports spaces in last name
    ifs>>acc.balance;
    ifs.ignore();                   // skip '\n' after balance
    return ifs;
}
ostream & operator<<(ostream &os,Account &acc)
{
    os<<"First Name: "<<acc.getFirstName()<<endl;
    os<<"Last Name: "<<acc.getLastName()<<endl;
    os<<"Account Number: "<<acc.getAccNo()<<endl;
    os<<"Balance: "<<fixed<<setprecision(2)<<acc.getBalance()<<endl;
    return os;
}

Bank::Bank()
{
    Account account;
    ifstream infile;

    infile.open("Bank.data");
    if(!infile)
    {
        //cout<<"Error in Opening! File Not Found!!"<<endl;
        return;
    }
    while(!infile.eof())
    {
        infile>>account;
        accounts.insert(pair<long,Account>(account.getAccNo(),account));
    }
    Account::setLastAccountNumber(account.getAccNo());

    infile.close();
}

Account Bank::OpenAccount(string fname,string lname,float balance)
{
    ofstream outfile;
    Account account(fname,lname,balance);
    accounts.insert(pair<long,Account>(account.getAccNo(),account));

    outfile.open("Bank.data", ios::trunc);

    map<long,Account>::iterator itr;
    for(itr=accounts.begin();itr!=accounts.end();itr++)
    {
        outfile<<itr->second;
    }
    outfile.close();
    return account;
}
Account Bank::BalanceEnquiry(long accountNumber)
{
    map<long,Account>::iterator itr=accounts.find(accountNumber);
    return itr->second;
}
Account Bank::Deposit(long accountNumber,float amount)
{
    map<long,Account>::iterator itr=accounts.find(accountNumber);
    itr->second.Deposit(amount);
    return itr->second;
}
Account Bank::Withdraw(long accountNumber,float amount)
{
    map<long,Account>::iterator itr=accounts.find(accountNumber);
    itr->second.Withdraw(amount);
    return itr->second;
}
void Bank::CloseAccount(long accountNumber)
{
    map<long,Account>::iterator itr=accounts.find(accountNumber);
    cout<<"Account Deleted"<<itr->second;
    accounts.erase(accountNumber);
}
void Bank::ShowAllAccounts()
{
    map<long,Account>::iterator itr;
    for(itr=accounts.begin();itr!=accounts.end();itr++)
    {
        cout<<"Account "<<itr->first<<endl<<itr->second<<endl;
    }
}
Bank::~Bank()
{
    ofstream outfile;
    outfile.open("Bank.data", ios::trunc);

    map<long,Account>::iterator itr;
    for(itr=accounts.begin();itr!=accounts.end();itr++)
    {
        outfile<<itr->second;
    }
    outfile.close();
}
