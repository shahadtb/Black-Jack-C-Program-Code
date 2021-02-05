# Black-Jack-C-Program-Code
An Electrical Computer Engineering project where the assignment was to manipulate the game of Black Jack into a C program code with any appropriate choice of logic

#define _CRT_SECURE_NO_WARNINGS
#define SHUFFLECOUNT 10000          //Number of times to shuffle the deck before the cards are dealt

#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<time.h>

/*2.1 (a) - Representation of cards
 The struct below represents a card. The <suit> variable represents the suit, <face> represents the face,
 and <pos> represents the position of the card on the deck (which is represented as a linked list). The <next>
 pointer points to the next card on the deck. For the last card on the deck, <next> is NULL.
 */
typedef struct card_s
{
	int suit;       //0 for clubs, 1 for spade, 2 for hearts, 3 for diamonds
	int face;       //1 for ace, 2-10 for respective cards, 11 for J, 12 for Q, and 0 for K
	int pos;        //position in the deck - a value from 1 to 52 (inclusive).
	struct card_s* next;
}card;

/*The function calculates the points won by the player <p>
 The cards[][] array describes the details of which player has which card/
 card[i][j] = p if player p has card(i,j). i is the suit (0-3) and j is the face (0-12).
 This function checks for a royal flush, a straight flush,..., a pair of jacks in that order (as
 given in the assignment).
 */

int points(int p, int cards[][13])
{
	//Check for royal flush.
	for (int i = 0; i < 4; i++)
		if ((cards[i][0] == p) && (cards[i][1] == p) && (cards[i][10] == p) && (cards[i][11] == p) && (cards[i][12] == p))
			return 9;

	//Check for straight flush
	for (int i = 0; i < 4; i++)
		if ((cards[i][6] == p) && (cards[i][7] == p) && (cards[i][8] == p) && (cards[i][9] == p) && (cards[i][10] == p))
			return 8;

	//Check for four-of-a-kind
	for (int j = 0; j < 13; ++j)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		if (count == 4)
			return 7;
	}

	//Check for full house
	int r = 0, s = 0;
	for (int j = 0; j < 13; ++j)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		if (count == 3)    //3 cards of one-kind found
			r = 1;
		if (count == 2)    //2 cards of one-kind found
			s = 1;
	}

	if ((r == 1) && (s == 1))
		return 6;

	//Check for flush
	for (int i = 0; i < 4; i++)
	{
		int count = 0;
		for (int j = 0; j < 13; j++)
			if (cards[i][j] == p)
				++count;
		if (count == 5)
			return 5;
	}

	//Check for straight
	int straight[13] = { 0 };
	for (int j = 0; j < 13; ++j)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		straight[j] = count;
	}

	//Look for five consecutive ones
	for (int k = 0; k < 8; k++)
		if ((straight[k] == 1) && (straight[k + 1] == 1) && (straight[k + 2] == 1) && (straight[k + 3] == 1) && (straight[k + 4] == 1))
			return 4;


	//Check for three-of-a-kind
	for (int j = 0; j < 13; ++j)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		if (count == 3)
			return 3;
	}

	//Check for two pairs
	int pair = 0;
	for (int j = 0; j < 13; ++j)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		if (count == 2)
			++pair;
	}
	if (pair == 2)
		return 2;

	//Check for jack or better
	for (int j = 0; j < 13;)
	{
		int count = 0;
		for (int i = 0; i < 4; i++)
			if (cards[i][j] == p)
				++count;
		if (count == 2)
			return 1;   //pair found

		//Look only for J,K,Q,A

		if (j == 0)
			j = 1;
		if (j == 1)
			j = 11;
		if (j == 11)
			j = 12;
		if (j == 12)   //exit the loop
			j = 13;
	}

	return 0;
}

/*
 This function is invoked at the end by the main() to decide which player
 has won the game. The points scored by both players p and q are computed
 by invoking the points() function, and then the appropriate message is
 displayed.
 */


void placebet(int cards[4][13])
{
	//Declare the winner.
	int p = points(1, cards);        //Find the number of points scored by player 1.
	int q = points(2, cards);        //Find the number of points scored by player 2.
	if (p > q)                         //Player 1 wins if he has scored more.
		printf("\nYou win!\n");
	else
		printf("\nComputer wins!\n");
}

/* The function below displays a particular card -
 "Jack of Clubs","4 of Diamonds" etc.
 */

void disp(struct card_s* c)                 //Function to display the details of a card.
{
	switch (c->face)                         //Print the face
	{
	case 1: printf("A"); break;
	case 11: printf("J"); break;
	case 12: printf("Q"); break;
	case 0: printf("K"); break;
	default: printf("%d", c->face);
	}
	switch (c->suit)                         //Print the suit
	{
	case 0: printf("%c", 5); break;
	case 1: printf("%c", 6); break;
	case 2: printf("%c", 3); break;
	case 3: printf("%c", 4); break;
	}
}

/* The display() function displays the first <count>
 cards in a list pointed to by the pointer <ptr>
 */

void display(struct card_s* ptr, int count)     //Function to display the first <count> cards of a list
{                                               //pointed to by the pointer <ptr>
	int i = 1;
	while ((i <= count) && (ptr != NULL))
	{
		printf("\n%d. ", i);
		disp(ptr);                              //Print the details of the particular card.
		ptr = ptr->next;
		++i;
	}
}

/* The swap function swaps the cards pointed to by pointers <ptr1>
 and <ptr2>.
 */

void swap(struct card_s* ptr1, struct card_s* ptr2) //Swap two cards being pointed to by the pointers
{
	int temp;                                       //Change the suit details
	temp = ptr1->suit;
	ptr1->suit = ptr2->suit;
	ptr2->suit = temp;

	temp = ptr1->face;                              //Change the face details
	ptr1->face = ptr2->face;
	ptr2->face = temp;

	temp = ptr1->pos;                               //Change the pos details
	ptr1->pos = ptr2->pos;
	ptr2->pos = temp;
}

/*
 The shuffle function shuffles a card as described in the assignment. It swaps
 the ith card with a random card (0-51) for all i in (0-51). This function is invoked
 at least 10,000 times from the main().
 */

void shuffle(struct card_s* deck[52])       //Shuffle a deck of cards
{
	int ran;
	for (int i = 0; i < 52; i++)
	{
		ran = rand() % 52;                    //Generate a random number (<ran>) between [0-51]
		swap(deck[i], deck[ran]);            //Interchange the ith card with the <ran>th card

	}
}

/* This function deletes a card from the beginning of the list,
 and returns the head of the new list.
 */

struct card_s* delete_card(struct card_s* head)
{
	if (head == NULL)                          //Nothing to delete.
		return NULL;

	struct card_s* temp = head->next;
	free(head);                             //Delete the first card
	return temp;
}

void begin_print(char *userName) {
	printf("|--------- -------- --------- --------- ---------|\n");
	printf("|--------- -------- --------- --------- ---------|\n");
	printf("\t%s, Let's play Jacks or Better\n", userName);
	printf("|--------- -------- --------- --------- ---------|\n");
	printf("\t\tRank of winning\n");
	printf("|--------- -------- --------- --------- ---------|\n");
	printf("|--------- -------- --------- --------- ---------|\n");
	printf("Royal Flush\t\t10%c J%c Q%c K%c A%c\t\t------------------------------------------------\n", 6, 6, 6, 6, 6);
	printf("Straight Flush\t\t2%c 3%c 4%c 5%c 6%c\t\t|# is used to represent any card number or suit|\n", 5, 5, 5, 5, 5);
	printf("Four of a Kind\t\t9%c 9%c 9%c 9%c #\t\t------------------------------------------------\n", 5, 6, 3, 4);
	printf("Full House\t\t9%c 9%c 9%c 3%c 3%c\t\t\n", 5, 6, 3, 3, 6);
	printf("Flush\t\t\t#%c #%c #%c #%c #%c\n", 3, 3, 3, 3, 3);
	printf("Straight\t\t4# 5# 6# 7# 8#\n");
	printf("Three of a Kind\t\t9%c 9%c 9%c # #\n", 5, 6, 3);
	printf("Two Pair\t\tK%c K%c 6%c 6%c #\n", 6, 5, 6, 3);
	printf("Jacks or better\t\tJ%c J%c # # #\n", 5, 6);
}
void print_coin(char *userName, int coins) {
	printf("\n********* *********  ********* *********  *********\n");
	printf("********* %s, you have %d coins *********\n", userName, coins);
	printf("********* *********  ********* *********  *********\n");
}
int coins(int *mula, int bet) {
	if (bet == -1) {
		*mula = *mula + 1 - bet;
	}
	else {
		*mula = *mula - bet;
	}

	return *mula;
}
void bet_bid(int *money, int *userBet) {
	while (*userBet < 1 || *userBet > *money) {
		printf("\nPlace your bet (1 - %d) coins: ", *money);
		scanf("%d", &*userBet);
	}
	coins(&*money, *userBet);
	printf("\nYou bet %d coin(s). Now you have %d coin(s).\n\n", *userBet, *money);
}

/* The main function creates all possible suit-face combinations (52 of them), then makes a linked list
 by pointing the <next> pointer of each card to another card. It then invokes the shuffle function
 10,000 times to shuffle the deck. It then, deals the first ten cards to the players - the user and the computer -
 and asks the player if he wishes to discard cards. Finally, the scores are computed.
 */

int main(void) {
	char userName = 'name', newGame = 'y';
	int userBet = -1, money = 100, cardHold = 6, i = 0, count;
	while (newGame != 'q') {
		printf("****************************************************\n");
		printf("*                                    ___           *\n");
		printf("*    _____     _        ____ ||  /  /              *\n");
		printf("*     | |     //\\     /      || /   \\              *\n");
		printf("*     | |    //  \\   ||      ||/     \\             *\n");
		printf("*     | |   //____\\  ||      ||\\      \\            *\n");
		printf("*     | |  //      \\  \\      || \\     /            *\n");
		printf("*    _/_/ //        \\  ----  ||  \\ __/             *\n");
		printf("****************************************** OR ******\n");
		printf("* _______   ______ ______ ______  ______  _____    *\n");
		printf("* ||    // ||        ||     ||   ||      ||   /    *\n");
		printf("* ||___//  ||        ||     ||   ||      ||  /     *\n");
		printf("* ||   \\   -------   ||     ||   ------  ||--\\     *\n");
		printf("* ||    \\  ||        ||     ||   ||      ||   \\    *\n");
		printf("*  ------  -------   ||     ||    ------ ||    \\   *\n");
		printf("****************************************************\n");
		printf("\nEnter your name: ");
		scanf("%s", &userName);
		begin_print(&userName);
		//2.1 (b) - creating a dynamic list of cards
		struct card_s* head = NULL;             //A pointer pointing to the head of the deck
		struct card_s* deck[52];                //An array of pointers
		for (int i = 0; i <= 3; ++i)
			for (int j = 0; j <= 12; j++)              //Generate all posible suit-face combinations [0-3]X[0-12]
			{
				struct card_s* newcardptr = malloc(sizeof(struct card_s));  //Allocate memory
				newcardptr->suit = i;
				newcardptr->face = j;
				newcardptr->pos = (i * 13) + j;     //Position of the card in the deck
				deck[(i * 13) + j] = newcardptr;    //Make the pointer at that location in the array point to the card
			}

		head = deck[0];
		for (int i = 0; i < 51; i++)               //Form the linked list
			deck[i]->next = deck[i + 1];
		deck[51]->next = NULL;

		//2.1 (c) - Shuffle the cards at least 10,000 times
		srand(time(0));

		for (int i = 1; i <= SHUFFLECOUNT; ++i)
			shuffle(deck);

		int cards[4][52];                   //A 2X2 array representing the deck.

		for (int i = 0; i < 4; i++)
			for (int j = 0; j < 52; j++)
				cards[i][j] = 0;              // Set card(i,j)=0 if the card has not been dealt
		while (money > 0) {
			userBet = -1;
			//2.1 (d) - Deal the cards - 5 each to the player and to the computer
			struct card_s* p = NULL;        //pointer to the head of a list of cards for the player
			struct card_s* c = NULL;        //pointer to the head of a list of cards for the computer

			/*Deal the first ten cards in the deck to the player and the computer
			 in an alternating fashion.*/

			for (int i = 0; i < 10 && head != NULL; i++)
			{
				struct card_s* newptr = malloc(sizeof(struct card_s));  //create a new card
				newptr->suit = head->suit;                              //Fill in the details of the card
				newptr->face = head->face;
				newptr->next = NULL;
				if (i % 2 == 0)                      //The card goes to the player
				{
					if (p == NULL)
						p = newptr;
					else
					{
						newptr->next = p;
						p = newptr;
					}
					cards[newptr->suit][newptr->face] = 1;        //The card has been dealt to the player
				}
				else
				{
					if (c == NULL)
						c = newptr;
					else
					{
						newptr->next = c;
						c = newptr;
					}
					cards[newptr->suit][newptr->face] = 2;        //The card goes to the computer
				}
				head = delete_card(head);                       //Delete the card from the original deck
			}
			printf("\n");
			print_coin(&userName, money);
			printf("\n");

			bet_bid(&money, &userBet);
			//Print the cards given to the player and to the computer
			printf("\nComputer's hand : \n");
			display(c, 3);
			printf("\n\nYour hand : \n");
			display(p, 5);
			int choice = 1;  //Variable to accept user's choice from STDIN
			do {
				count = 0;
				while ((count < 5) && (count >= 0) && (1 <= choice) && (choice <= 5)) {
					printf("\n\nEnter the card (1-5) you wish to discard (any other number to not discard any card): ");
					scanf("%d", &choice);
					if ((1 <= choice) && (choice <= 5))
					{
						//Get a new card and add it to the head
						struct card_s* newptr = malloc(sizeof(struct card_s));
						newptr->suit = head->suit;
						newptr->face = head->face;
						newptr->next = p;
						p = newptr;
						cards[newptr->suit][newptr->face] = 1;        //Deal a card to the player
						head = delete_card(head);                   //Delete the card from the deck

						//discard the appropriate card
						struct card_s* tempptr = p;
						struct card_s* tempptr2 = p;
						for (int i = 0; i < choice; ++i)
						{
							tempptr2 = tempptr;
							tempptr = tempptr->next;
						}

						//Delete the card the player wanted to discard
						tempptr2->next = tempptr->next;

						//tempptr is pointing to the node that must be discarded
						cards[tempptr->suit][tempptr->face] = 0;
						free(tempptr);
						if (count != 4) {
							printf("\nComputer's hand : \n");
							display(c, 3);
						}
						else if (choice < 1 || choice > 5) {
							printf("\nComputer's hand : \n");
							display(c, 5);
						}
						if (count == 4) {
							printf("\nComputer's hand : \n");
							display(c, 5);
						}
						printf("\n\nYour hand : \n");
						display(p, 5);

					}
					count = count + 1;
					//Decide the winner by comparing the points of the player and the computer.
				}
				if (count == 4 || (choice < 1 || choice > 5)) {
					printf("\nComputer's hand : \n");
					display(c, 5);
					printf("\n\nYour hand : \n");
					display(p, 5);
				}
				getchar();
				printf("\n");
				placebet(cards);
				
				int p = points(1, &cards);        //Find the number of points scored by player 1.
				int q = points(2, &cards);        //Find the number of points scored by player 2.
				if (p > q) {
					money = money + (2 * userBet);
					printf("\nYou have %d coins now!\nPress 'Enter' to continue.\n", money);
				}
				else {
					printf("\nYou remain with %d coins.\nPress 'Enter' to continue.\n", money);
				}
				break;
			} while ((1 <= choice) && (choice <= 5));    //Do this as long as the player wants to discard cards.
			getchar();
			printf("\n");
			placebet(cards);
			
			int x = points(1, &cards);        //Find the number of points scored by player 1.
			int y = points(2, &cards);        //Find the number of points scored by player 2.
			if (x > y) {
				money = money + (2 * userBet);
				printf("\nYou have %d coins now!\nPress 'Enter' to continue.\n", money);
			}
			else if (count == 4) {
				printf("\nYou remain with %d coins.\nPress 'Enter' to continue.\n", money);
			}
		}
		printf("\nNew Game (q to quit)? ");
		scanf(" %c", &newGame);

	}
	return 0;
}
