#include <iostream>
#include <cstring>
#include <cstdlib>
#include <ctime>
#include<fstream>
#include <thread>
#include <chrono>
#ifdef _WIN32
#include <windows.h>
#endif
using namespace std;

#ifdef _WIN32
void enableANSIColors(){
    HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
    if(hOut == INVALID_HANDLE_VALUE) return;

    DWORD mode = 0;
    if(GetConsoleMode(hOut, &mode)){
        SetConsoleMode(hOut, mode | ENABLE_VIRTUAL_TERMINAL_PROCESSING);
    }
}
#else
void enableANSIColors(){}
#endif


// Cross-platform console colors.
// The original program used Windows-only SetConsoleTextAttribute().
void setColor(int color){
    // Convert the original Windows color numbers used by the program
    // to ANSI escape colors, which work on macOS and modern Windows.
    switch(color){
        case 10: cout << "\033[32m"; break; // Green
        case 12: cout << "\033[31m"; break; // Red
        case 11: cout << "\033[36m"; break; // Cyan
        case 14: cout << "\033[33m"; break; // Yellow
        case 7:  cout << "\033[0m";  break; // Reset
        default: cout << "\033[0m"; break;
    }
}

// Cross-platform screen clearing.
void clearScreen(){
    cout << "\033[2J\033[1;1H";
}

void wait(){
	cout<<"\nPress any key to continue...";
	cin.ignore();
	cin.get();
}

void loading(string text){
	cout<<text;

	for(int i=0;i<3;i++){
		cout<<".";
		cout.flush();
		this_thread::sleep_for(chrono::milliseconds(500));
	}

	cout<<endl;
}

class Player {
    string name;
    float chips;

public:
    Player(string name) {
        this->name = name;
        chips = 100.00;
    }

    float getChips() {
        return chips;
    }

    void addChips(float amount) {
        chips += amount;
    }

    void removeChips(float amount) {
        chips -= amount;
    }
    void setChips(float chips){
	this->chips=chips;
}
};




class CasinoMachine{
	protected:
	float multiplier;
	float insertedChips;
	
	
	int AskChips(string MachineName,Player& player){
	int chips;
	cout << "==========================================" << endl;
    cout << "             "<<MachineName; cout<<"                 " << endl;
    cout << "==========================================" << endl;
    cout << "  Chips Balance: $=" << player.getChips() << endl;
    cout << "==========================================" << endl;
    cout << "Enter the amount you want to bet: ";
    cin>>chips;
    return chips;
}

};
class SlotMachine:CasinoMachine{
	Player& player;

	string getSymbol(int &value){
		value=rand()%100;

		if(value<40)
			return "Cherry";
		else if(value<65)
			return "Bell";
		else if(value<85)
			return "BAR";
		else if(value<95)
			return "Diamond";
		else
			return "7";
	}

	void play(){
		int s1,s2,s3;

		string r1=getSymbol(s1);
		string r2=getSymbol(s2);
		string r3=getSymbol(s3);

		cout<<"\n===================="<<endl;
		cout<<"      SLOT SPIN      "<<endl;
		cout<<"===================="<<endl;

		cout<<"["<<r1<<"] ["<<r2<<"] ["<<r3<<"]"<<endl;

		float win=0;

		if(r1=="7"&&r2=="7"&&r3=="7"){
			win=insertedChips*50;
			cout<<"\nJACKPOT!!! THREE 7s!"<<endl;
		}
		else if(r1==r2&&r2==r3){
			if(r1=="Diamond")
				win=insertedChips*20;
			else if(r1=="BAR")
				win=insertedChips*15;
			else if(r1=="Bell")
				win=insertedChips*8;
			else if(r1=="Cherry")
				win=insertedChips*5;

			cout<<"\nThree matching symbols!"<<endl;
		}
		else if(r1==r2||r2==r3||r1==r3){
			win=insertedChips*2;
			cout<<"\nTwo matching symbols! Small win!"<<endl;
		}

		if(win>0){
			cout<<"You won "<<win<<" chips!"<<endl;
			player.addChips(win);
		}else{
			cout<<"No match. You lost!"<<endl;
		}
	}

public:
	SlotMachine(Player& player):player(player){
		multiplier=3;
	}

	void initialize(){
		char start;

		insertedChips=AskChips("SLOT MACHINE",player);

		cout<<"You bet "<<insertedChips<<" chips"<<endl;
		cout<<"Spin the slots? (Y/N): ";
		cin>>start;

		if(start=='Y'||start=='y'){

			clearScreen();

			loading("Spinning slots");

			player.removeChips(insertedChips);

			cout<<"\nSpinning";
			for(int i=0;i<3;i++){
				cout<<".";
			}
			cout<<endl;

			play();

			char hold;
			cout<<"\nEnter any character to return: ";
			cin>>hold;
		}
	}
};

class RockPaperScissors:CasinoMachine {
	Player& player;
	void play(){
		int userChoice;
		cout<<"1. Rock, 2. Paper, 3. Scissors: ";
		cin>>userChoice;
		int compChoice=rand()%3+1;
		string choices[]={"","Rock","Paper","Scissors"};
		cout<<"Computer chose: "<<choices[compChoice]<<endl;
		if(userChoice==compChoice){
			cout<<"It's a tie! Chips returned."<<endl;
			player.addChips(insertedChips);
		}else if((userChoice==1&&compChoice==3)||(userChoice==2&&compChoice==1)||(userChoice==3&&compChoice==2)){
			float win=insertedChips*multiplier;
			setColor(10);
			cout<<"YOU WON "<<win<<" CHIPS!"<<endl;
			setColor(7);
			player.addChips(win);
		}else{
			setColor(12);
			cout<<"YOU LOST!"<<endl;
			setColor(7);
		}
	}
public:
	RockPaperScissors(Player& player):player(player){
		this->multiplier=2;
	}
    void initialize() {
        char start;
		this->insertedChips=AskChips("Rock Paper Scissor",player);
		cout<<"You have bet "<<insertedChips<<" chips"<<endl;
		cout<<"Spin the SoltMachine?(Y/N):";
		cin>>start;
		if(start=='Y'||start=='y'){
			player.removeChips(insertedChips);
			play();
			char hold;
			cout<<"Enter any character to return to floor: ";
			cin>>hold;
		}else{
			return;
		}
    }
};

class Blackjack:CasinoMachine{
	Player& player;

	int drawCard(string &name){
		int card=rand()%13+1;
		if(card==1){
			name="A";
			return 11;
		}
		if(card==11){
			name="J";
			return 10;
		}
		if(card==12){
			name="Q";
			return 10;
		}
		if(card==13){
			name="K";
			return 10;
		}
		char c=card+'0';
		name=c;
		return card;
	}

	void adjustAce(int &total,int &aces){
		while(total>21&&aces>0){
			total-=10;
			aces--;
		}
	}

	void play(){
		string p1Name,p2Name,d1Name,d2Name,name;
		int playerTotal=0,dealerTotal=0;
		int playerAces=0,dealerAces=0;

		int p1=drawCard(p1Name);
		if(p1==11)playerAces++;
		playerTotal+=p1;

		int p2=drawCard(p2Name);
		if(p2==11)playerAces++;
		playerTotal+=p2;

		int d1=drawCard(d1Name);
		if(d1==11)dealerAces++;
		dealerTotal+=d1;

		int d2=drawCard(d2Name);
		if(d2==11)dealerAces++;
		dealerTotal+=d2;

		adjustAce(playerTotal,playerAces);
		adjustAce(dealerTotal,dealerAces);

		cout<<"\nShuffling deck...\n"<<endl;
		cout<<"Dealer: ["<<d1Name<<"] [?]"<<endl;
		cout<<"You: ["<<p1Name<<"] ["<<p2Name<<"]"<<endl;
		cout<<"Total: "<<playerTotal;
		if(playerAces>0)cout<<" (Soft)";
		cout<<endl;

		bool playerBJ=(playerTotal==21&&p1+p2==21);
		bool dealerBJ=(dealerTotal==21&&d1+d2==21);

		if(playerBJ||dealerBJ){
			cout<<"\nDealer reveals: ["<<d1Name<<"] ["<<d2Name<<"]"<<endl;
			cout<<"Dealer total: "<<dealerTotal<<endl;

			if(playerBJ&&!dealerBJ){
				float win=insertedChips*2.5;
				cout<<"\n*** BLACKJACK! ***"<<endl;
				cout<<"You win "<<win<<" chips!"<<endl;
				player.addChips(win);
			}else if(playerBJ&&dealerBJ){
				cout<<"Both have Blackjack! Push!"<<endl;
				player.addChips(insertedChips);
			}else{
				cout<<"Dealer has Blackjack!"<<endl;
				setColor(12);
cout<<"YOU LOST!"<<endl;
setColor(7);
			}
			return;
		}

		char choice;

		while(playerTotal<21){
			cout<<"\n(H) Hit"<<endl;
			cout<<"(S) Stand"<<endl;
			cout<<"(D) Double Down"<<endl;
			cout<<"Choice: ";
			cin>>choice;

			if(choice=='H'||choice=='h'){
				int card=drawCard(name);
				if(card==11)playerAces++;
				playerTotal+=card;
				adjustAce(playerTotal,playerAces);

				cout<<"You drew "<<name<<endl;
				cout<<"Total: "<<playerTotal;
				if(playerAces>0)cout<<" (Soft)";
				cout<<endl;
			}else if(choice=='D'||choice=='d'){
				if(player.getChips()>=insertedChips){
					player.removeChips(insertedChips);
					insertedChips*=2;

					int card=drawCard(name);
					if(card==11)playerAces++;
					playerTotal+=card;
					adjustAce(playerTotal,playerAces);

					cout<<"Bet doubled to "<<insertedChips<<" chips."<<endl;
					cout<<"You drew "<<name<<endl;
					cout<<"Total: "<<playerTotal<<endl;
					break;
				}else{
					cout<<"Not enough chips to double down."<<endl;
				}
			}else{
				break;
			}
		}

		if(playerTotal>21){
			cout<<"\nBust! Better luck next round!"<<endl;
			return;
		}

		cout<<"\nDealer reveals: ["<<d1Name<<"] ["<<d2Name<<"]"<<endl;
		cout<<"Dealer total: "<<dealerTotal<<endl;

		while(dealerTotal<17){
			int card=drawCard(name);
			if(card==11)dealerAces++;
			dealerTotal+=card;
			adjustAce(dealerTotal,dealerAces);

			cout<<"Dealer draws "<<name<<endl;
			cout<<"Dealer total: "<<dealerTotal<<endl;
		}

		cout<<endl;

		if(dealerTotal>21){
			float win=insertedChips*multiplier;
			cout<<"Dealer Busts!"<<endl;
			cout<<"You win "<<win<<" chips!"<<endl;
			player.addChips(win);
		}else if(playerTotal>dealerTotal){
			float win=insertedChips*multiplier;
			cout<<"You win "<<win<<" chips!"<<endl;
			player.addChips(win);
		}else if(playerTotal==dealerTotal){
			cout<<"Push! Your bet has been returned."<<endl;
			player.addChips(insertedChips);
		}else{
			cout<<"Dealer wins!"<<endl;
		}
	}

public:
	Blackjack(Player& player):player(player){
		multiplier=2;
	}

	void initialize(){
		char start;
		insertedChips=AskChips("BLACKJACK",player);

		cout<<"You bet "<<insertedChips<<" chips"<<endl;
		cout<<"Play Blackjack? (Y/N): ";
		cin>>start;

		if(start=='Y'||start=='y'){
			player.removeChips(insertedChips);
			play();
			char hold;
			cout<<"\nEnter any character to return: ";
			cin>>hold;
		}
	}
};
class Roulette:CasinoMachine{
	Player& player;

	bool isRed(int n){
		int red[]={1,3,5,7,9,12,14,16,18,19,21,23,25,27,30,32,34,36};
		for(int i=0;i<18;i++){
			if(red[i]==n)return true;
		}
		return false;
	}

	void play(){
		int choice;

		cout<<"\n====== ROULETTE ======"<<endl;
		cout<<"1. Single Number (35:1)"<<endl;
		cout<<"2. Red (1:1)"<<endl;
		cout<<"3. Black (1:1)"<<endl;
		cout<<"4. Even (1:1)"<<endl;
		cout<<"5. Odd (1:1)"<<endl;
		cout<<"6. Low (1-18)"<<endl;
		cout<<"7. High (19-36)"<<endl;
		cout<<"Choice: ";
		cin>>choice;

		int numberBet=-1;

		if(choice==1){
			cout<<"Choose a number (0-36): ";
			cin>>numberBet;
		}

		int wheel=rand()%37;

		cout<<"\nWheel landed on "<<wheel;

		if(wheel!=0){
			if(isRed(wheel))
				cout<<" (Red)";
			else
				cout<<" (Black)";
		}else{
			cout<<" (Green)";
		}

		cout<<endl;

		bool win=false;

		switch(choice){
			case 1:
				if(numberBet==wheel){
					multiplier=36;
					win=true;
				}
				break;

			case 2:
				if(wheel!=0&&isRed(wheel)){
					multiplier=2;
					win=true;
				}
				break;

			case 3:
				if(wheel!=0&&!isRed(wheel)){
					multiplier=2;
					win=true;
				}
				break;

			case 4:
				if(wheel!=0&&wheel%2==0){
					multiplier=2;
					win=true;
				}
				break;

			case 5:
				if(wheel%2==1){
					multiplier=2;
					win=true;
				}
				break;

			case 6:
				if(wheel>=1&&wheel<=18){
					multiplier=2;
					win=true;
				}
				break;

			case 7:
				if(wheel>=19&&wheel<=36){
					multiplier=2;
					win=true;
				}
				break;

			default:
				cout<<"Invalid choice!"<<endl;
				player.addChips(insertedChips);
				return;
		}

		if(win){
			float winnings=insertedChips*multiplier;
			cout<<"Congratulations! You won "<<winnings<<" chips!"<<endl;
			player.addChips(winnings);
		}else{
			cout<<"Better luck next time!"<<endl;
		}
	}

public:
	Roulette(Player& player):player(player){
		multiplier=2;
	}

	void initialize(){
		char start;
		insertedChips=AskChips("ROULETTE",player);

		cout<<"You bet "<<insertedChips<<" chips"<<endl;
		cout<<"Spin the wheel? (Y/N): ";
		cin>>start;

		if(start=='Y'||start=='y'){
			player.removeChips(insertedChips);
			play();
			char hold;
			cout<<"\nEnter any character to return: ";
			cin>>hold;
		}
	}
};
class Crash:CasinoMachine{
	Player& player;

	void play(){
		float multiplier=1.00;
		float crashPoint;

		int chance=rand()%100;

		if(chance<50){
			crashPoint=(rand()%100+101)/100.0;   
		}
		else if(chance<85){
			crashPoint=(rand()%200+201)/100.0;   
		}
		else if(chance<97){
			crashPoint=(rand()%600+401)/100.0;   
		}
		else{
			crashPoint=(rand()%4000+1001)/100.0; 
		}
		char choice;

		cout<<"\n========== CRASH =========="<<endl;
		cout<<"Game started!"<<endl;
		cout<<"Cash out before the multiplier crashes!"<<endl;

		while(true){
			multiplier+=0.10;

			cout<<"\nCurrent multiplier: "<<multiplier<<"x"<<endl;
			cout<<"Cash out? (Y/N): ";
			cin>>choice;

			if(choice=='Y'||choice=='y'){
				float win=insertedChips*multiplier;
				cout<<"You cashed out at "<<multiplier<<"x!"<<endl;
				cout<<"You won "<<win<<" chips!"<<endl;
				player.addChips(win);
				return;
			}

			if(multiplier>=crashPoint){
				cout<<"\n?? CRASH!"<<endl;
				cout<<"The multiplier exploded at "<<crashPoint<<"x"<<endl;
				cout<<"You lost "<<insertedChips<<" chips!"<<endl;
				return;
			}
		}
	}

public:
	Crash(Player& player):player(player){
		multiplier=1;
	}

	void initialize(){
		char start;

		insertedChips=AskChips("CRASH",player);

		cout<<"You bet "<<insertedChips<<" chips"<<endl;
		cout<<"Start Crash? (Y/N): ";
		cin>>start;

		if(start=='Y'||start=='y'){
			player.removeChips(insertedChips);
			play();

			char hold;
			cout<<"\nEnter any character to return: ";
			cin>>hold;
		}
	}
};


void showMainMenu(Player& player){

	clearScreen();

	setColor(11);

	cout<<"=========================================="<<endl;
	cout<<"              ROYAL CASINO                "<<endl;
	cout<<"=========================================="<<endl;

	setColor(7);

	cout<<" Player Balance : "<<player.getChips()<<" chips"<<endl;

	cout<<"------------------------------------------"<<endl;

	cout<<" 1. Enter Casino Floor"<<endl;
	cout<<" 2. View High Scores"<<endl;
	cout<<" 3. Exit Casino"<<endl;

	cout<<"=========================================="<<endl;
	cout<<" Choice: ";

}

void showCasinoMenu(Player& player){

	clearScreen();

	setColor(14);

	cout<<"=========================================="<<endl;
	cout<<"              CASINO FLOOR                "<<endl;
	cout<<"=========================================="<<endl;

	setColor(7);

	cout<<"Balance: "<<player.getChips()<<" chips"<<endl;

	cout<<"------------------------------------------"<<endl;

	cout<<" 1.  Slot Machine"<<endl;
	cout<<" 2.  Rock Paper Scissors"<<endl;
	cout<<" 3.  Blackjack"<<endl;
	cout<<" 4.  Roulette"<<endl;
	cout<<" 5.  Crash"<<endl;
	cout<<" 6.  Return"<<endl;

	cout<<"=========================================="<<endl;
	cout<<"Select game: ";

}
void casinoFloor(Player& player) {
    int gameChoice = 0;
    
    SlotMachine slots(player);
    RockPaperScissors rps(player);
    Blackjack blackjack(player);
    Roulette roulette(player);
    Crash crash(player);

    do {
        showCasinoMenu(player);
        cin >> gameChoice;

        if(player.getChips() <= 0){
            cout << "\nInsufficient Balance!" << endl;
            cout << "Your balance is 0 chips. Please exit the casino." << endl;

            char hold;
            cout << "\nPress any key to return: ";
            cin >> hold;
            return;
        }

        switch (gameChoice) {
            case 1:
                slots.initialize();
                break;
            case 2:
                rps.initialize();
                break;
            case 3:
                blackjack.initialize();
                break;
            case 4:
                roulette.initialize();
                break;
            case 5:
				crash.initialize();
				break;

			case 6:
				cout << "\nReturning to Main Menu...\n" << endl;
				break;
            default:
                cout << "\nInvalid choice! Select an option between 1 and 5.\n\n";
                break;
        }

    } while (gameChoice != 6);
}
void saveScore(string playerName,float chips){
	string names[100];
	float scores[100];
	int count=0;

	ifstream fin("scores.txt");

	while(fin>>names[count]>>scores[count]){
		count++;
	}

	fin.close();

	names[count]=playerName;
	scores[count]=chips;
	count++;

	for(int i=0;i<count-1;i++){
		for(int j=i+1;j<count;j++){
			if(scores[j]>scores[i]){
				float tempScore=scores[i];
				scores[i]=scores[j];
				scores[j]=tempScore;

				string tempName=names[i];
				names[i]=names[j];
				names[j]=tempName;
			}
		}
	}

	ofstream fout("scores.txt");

	for(int i=0;i<count;i++){
		fout<<names[i]<<" "<<scores[i]<<endl;
	}

	fout.close();
}

void showHighScores(){
	ifstream fin("scores.txt");

	string name;
	float chips;
	int rank=1;

	cout<<"\n========== HIGH SCORES ==========\n";

	while(fin>>name>>chips){
		cout<<rank<<". "<<name<<"\t"<<chips<<" chips"<<endl;
		rank++;
	}

	if(rank==1)
		cout<<"No scores found."<<endl;

	cout<<"=================================\n";

	fin.close();

	char hold;
	cout<<"Press any key: ";
	cin>>hold;
}

float loadScore(string playerName){
	ifstream fin("scores.txt");

	string name;
	float chips;

	while(fin>>name>>chips){
		if(name==playerName){
			fin.close();
			return chips;
		}
	}

	fin.close();
	return -1;
}

void removeOldScore(string playerName){
	string names[100];
	float scores[100];
	int count=0;

	ifstream fin("scores.txt");

	while(fin>>names[count]>>scores[count]){
		if(names[count]!=playerName)
			count++;
	}

	fin.close();

	ofstream fout("scores.txt");

	for(int i=0;i<count;i++)
		fout<<names[i]<<" "<<scores[i]<<endl;

	fout.close();
}

int main() {
    enableANSIColors();
	srand(time(0));
    int choice = 0;
    string playerName;

    int age;

    cout << "Enter your age: ";
    cin >> age;

    if(age < 18){
        cout << "\nAccess Denied!" << endl;
        cout << "You must be 18 or older to enter the casino." << endl;
        return 0;
    }

    cout << "Enter player name: ";
    cin >> playerName;

    Player player(playerName);

	float saved=loadScore(playerName);

	if(saved!=-1){
		char ch;
		cout<<"Welcome back "<<playerName<<"!"<<endl;
		cout<<"A saved game with "<<saved<<" chips was found."<<endl;
		cout<<"Continue? (Y/N): ";
		cin>>ch;

		if(ch=='Y'||ch=='y')
			player.setChips(saved);
	}

    do {
        showMainMenu(player);
        cin >> choice;


        switch(choice){
	case 1:
		casinoFloor(player);
		break;

	case 2:
		showHighScores();
		break;

	case 3:
		saveScore(playerName,player.getChips());
		cout<<"\nScore saved!"<<endl;
		cout<<"Thank you for playing, "<<playerName<<"!"<<endl;
		break;

	default:
		cout<<"Invalid choice!"<<endl;
}

    } while (choice != 3);

    return 0;
}
