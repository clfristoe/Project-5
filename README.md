	#include <iostream>
	#include <fstream>
	#include <iomanip>
	#include <ctype.h>
	using namespace std;

	void ReadInput(ifstream &, char*[], char*[], float [50][5], int &, int &);
	void PrintArray(ofstream &, char[], char*[], char*[], float [50][5], int &, int&);
	void PrintOutput(ofstream &, int &);
	void SortArray(char*[], char*[], float [50][5], int &, int &);
	void SortByAvg(char*[], char*[], float [50][5], char [], int &, int &);
	void FindAvg(float [50][5], int &, int &);
	void Letter(float [50][5], char [], int &, int &);
	void Footer(ofstream &);
	void Header(ofstream &);

	int main()
	{
	ifstream input ("DATA5.TXT", ios::in);
	ofstream output ("OUTPUT", ios::out);
	output.setf(ios::fixed);
	output.precision(2); //limit the decimal places after a decimal to two
	float testScores[50][5] = {0}; //create a multi-dimensional array for test scores
	char letter[50]; //create an array for letter grades
	char *FirstName[50];//create an array for first names
	char *LastName[50];//create an array for last names
	int REU; //keep track of how many rows are used in each array
	int CEU; //keep track of how many columns are used in each array
	int check; //keep a variable to check whether there is an average and letter grade

	Header(output); //print the header
	ReadInput(input, FirstName, LastName, testScores, REU, CEU); //read input from file

	output << setw(10)<<"The original test data is:"<< endl;
	output <<setw(15)<<"Student Name"<< endl;
	check = 0; //Don't print the average and letter grade columns
	PrintOutput(output, check); //Print the output header
	PrintArray(output, letter, FirstName, LastName, testScores,REU, check); //Print the original array
	output << endl;
	
	SortArray(FirstName, LastName, testScores, REU, CEU); //Sort the array by last name from A-Z
	output << setw(10)<<"The list of students sorted A thru Z by Last Name is:"<< endl;
	output <<setw(15)<<"Student Name"<< endl;
	check = 0; //Don't print the average and letter grade
	PrintOutput(output, check); //Print the output header
	PrintArray(output, letter, FirstName, LastName, testScores, REU, check); //Print the sorted array
	output << endl;
	

	check = 1; //Print out the average and letter grade columns
	FindAvg(testScores, REU, CEU); //Find the average of each row of test scores
	Letter(testScores, letter, REU, CEU); //Create an array with a letter grade for each average
	output <<setw(10)<<"The list of students with their test average and course grade is:"<< endl;
	output <<setw(14)<<"Student Name"<< endl;
	PrintOutput(output, check); //print the output header, including average and letter grade
	PrintArray(output, letter, FirstName, LastName, testScores, REU, check); //Print list with avg & grades
	output << endl;
	
	SortByAvg(FirstName, LastName, testScores, letter, REU, CEU); //Sort by test score from high to low
	output <<setw(10)<<"The list of students sorted by test average high to low is:"<< endl;
	output <<setw(14)<<"Student Name"<< endl;
	PrintOutput(output,check); //Print the output header, including average and letter grade
	PrintArray(output, letter, FirstName, LastName, testScores, REU, check); //Print the sorted list
	
	Footer(output); //Print the footer
	return 0;
	
	}


	void ReadInput(ifstream &infile,  char *First[], char *Last[], float scores[50][5], int &REU, int &CEU)
	{		//Recieves - the input file, array of first and last names and test scores, RU, CU
			//Task - reads in the first names, last names, and test scores from a file
			//Returns - filled arrays of first names, last names, and test scores
	
	char *newPtr; //create a temp array for the name
	char first[15], last[15]; //create temp variables for the first and last name
	int i;
	REU = 0; //initialize rows used to zero
	infile >> ws;
	infile.getline(first,15); //read first name from file
	while(strcmp(first,"No") != 0) // read in names and test scores until "No More"
	{
		newPtr = new char[15];//create a temp holding place for the incoming name
		strcpy(newPtr, first); //copy the first name into the temp array
		First[REU] = newPtr; //assign the index of the array of first names to the pointer location
		infile >> ws; 
		infile.getline(last,15); //read last name from file
		newPtr = new char[15]; //create a temp holding place for the incoming name
		strcpy(newPtr, last); //copy the last name into the temp array
		Last[REU] = newPtr; //assign the index of the array of first names to the pointer location
		CEU = 0; //Initialize the columns used for use in the next row
		for(i=0; i<4; i++) //loop through each column in file
			{
			infile >> scores[REU][i]; //read in the test score
			}
		REU++;
		infile >> ws;
		infile.getline(first,15); // read the next name from the file
		CEU=i;
	}
	return;
	}


	void PrintArray(ofstream &outfile, char l[], char *F[], char *L[], float s[50][5], int &REU, int &check)
			// Recieves - the output file, array of first and last names, array of test scores, and EU
			// Task - Prints the array of first and last names, and test scores in columns
			// Returns - Nothing
	{
	int i,j;
	for(i=0; i<REU; ++i) //continue in this loop until there are no more values
	{
		// print the student id's and test scores on the same line with equal spacing
		outfile << setw(6) << F[i]<<" "; //print out the ID number
		outfile << setw(6) << L[i]<<" ";
		if(check == 0)//print test scores without averages and letter grades if unavailable
		for(j=0;j<4;j++) // Loop through all columns of array
		outfile << setw(11)<< s[i][j]; //print out the test score
		if(check == 1)//print averages and letter grades if available
		{
			for(j=0;j<5;j++) // Loop through all columns of array
		outfile << setw(11)<< s[i][j]; //print out the test score
		outfile << setw(10)<< l[i]; //print out the letter grade
		}
		outfile << endl;
	}
	return;
	}


	void SortArray(char *First[], char *Last[], float scores[50][5], int &REU, int &CEU) 
			//Recieves - the first and last name arrays, test scores, rows used, and columns used
			//Task - sorts the parallel arrays based on last name from A to Z
			//Returns - the parallel arrays sorted from A to Z by last name
	{
	char *newPtr, *newPtr2; //initialize temp arrays for names
    int Row, N, col; //Initialize variables used for looping through both rows and columns
	float temp; //Initialize variables used for looping through both rows and columns
    for ( Row = 0 ; Row < REU-1; Row++) // Loop through every element in the array
     {
		 for ( N = REU-1; N > Row ; N--) // Test each element from the end to the beginning
		{
			if (strcmp(Last[N], Last[N-1]) < 0 ) // Compare each element and swap if necessary
			{
				newPtr = new char[15]; //make a temporary variable for the last name
				newPtr2 = new char[15]; //make a temporary variable for the first name
				strcpy(newPtr, Last[N]); //place the index of the last name in a temp variable
				strcpy(Last[N],Last[N-1]); // decrement the value of N for the last name
				strcpy(Last[N-1], newPtr); //place the new index of last name into the temp variable

				strcpy(newPtr2, First[N]); //place the index of the first name in a temp variable
				strcpy(First[N],First[N-1]); // decrement the value of N for the first name
				strcpy(First[N-1], newPtr2); //place the new index of first name into the temp var
				newPtr = 0; //initialize variable to zero for next use
				newPtr2 = 0; //initialize variable to zero for next use
				for(col=0; col<4; col++) //Loop through every column in each row
				{
				 temp = scores[N][col]; //place the index of the score in a temporary variable
				 scores[N][col] = scores[N-1][col]; // decrement the value of N for test scores
				 scores[N-1][col] = temp; //place the new index of scores into the temp variable
				}
			}
		}
     }
      return;  
	 } 


	void FindAvg(float scores[50][5], int &RU, int &CU)
			//Recieves - the test scores, rows used, and columns used
			//Task - computes the average for all the test scores in one row of the array
			//Returns - the array with the average test score in the fourth index of the scores
	{
	int row,col;
	float total;
	float avg;
	for(row = 0; row<RU; row++) //Loop through each row of the array of test scores
	{
		total = 0.0; //Zero the total for use in each row of the array
		for(col=0; col<CU; col++) //Loop through each column of the array of test scores
			total+=scores[row][col]; //Add the test score at each position to the total
			avg = total/CU; //Divide the total score by the columns used to find the average
			scores[row][4]=avg; //Add the average as the fourth element in the test score array
	}
	return;
	}


	void Letter(float testScores[50][5], char letter[], int &RU, int &CU)
			//Recieves - the test scores, letter grades, rows used, and columns used
			//Task - determines the letter grade for each ID number based on average test score
			//Returns - an array of letter grades parallel to each ID number and test scores
	{
	int row;
	for(row = 0; row<RU; row++)//Loop through each row of the array of test scores
	{
		if(testScores[row][4]>=90.0) //Print if the test score equals an A
			letter[row]='A';
		else if(testScores[row][4]>=80.0 && testScores[row][4]<90.0)//Print if score is a B
			letter[row]='B';
		else if(testScores[row][4]>=70.0 && testScores[row][4]<80.0)//Print if score is a C
			letter[row]='C';
		else if(testScores[row][4]>=60.0 && testScores[row][4]<70.0)//Print if score is a D
			letter[row]='D';
		else
			letter[row]='F'; //Print if score is an F
	}
	return;
	}


	void SortByAvg(char *First[], char *Last[], float scores[50][5], char letter[], int &REU, int &CEU)
			//Recieves - the first and last names, test scores, letter grades, rows and columns used
			//Task - sorts all four arrays based on average test score from high to low
			//Returns - the arrays sorted by average test score from high to low
	{
	char *newPtr, *newPtr2; //intialize arrays for names
    int Row, N, col; //Initialize variables used for looping through both rows and columns
	float temp, temp2; 
    for ( Row = 0 ; Row < REU-1; Row++) // Loop through every element in the array
     {
		 for ( N = REU-1; N > Row ; N--) // Test each element from the end to the beginning
		{
			if (scores[N][4] > scores[N-1][4]) // Compare each element and swap if necessary
			{
				newPtr = new char[15]; //make a temporary variable for the last name
				newPtr2 = new char[15]; //make a temporary variable for the first name
				strcpy(newPtr, Last[N]); //place the index of the last name in a temp variable
				strcpy(Last[N],Last[N-1]); // decrement the value of N for the last name
				strcpy(Last[N-1], newPtr); //place the new index of last name into the temp variable

				strcpy(newPtr2, First[N]); //place the index of the first name in a temp variable
				strcpy(First[N],First[N-1]); // decrement the value of N for the first name
				strcpy(First[N-1], newPtr2); //place the new index of first name into the temp var

				newPtr = 0; //initialize variable to zero for next use
				newPtr2 = 0; //initialize variable to zero for next use

				temp2 = letter[N]; //place the index of the letter grade in a temp variable
				letter[N] = letter[N-1]; //decrement the value of N for the letter grade
				letter[N-1] = temp2; //place the new index of the letter grade in a temp variable
				for(col=0; col<=4; col++) // Swap each column of elements accordingly
				{
				 temp = scores[N][col]; //place the index of the score in a temporary variable
				 scores[N][col] = scores[N-1][col]; // decrement the value of N for test scores
				 scores[N-1][col] = temp; //place the new index of scores into the temp variable
				}
			}
		}
     }
       return;  
 	} 


	void PrintOutput(ofstream &output, int &check)
	{		//Recieves - the output file and an integer to see which columns will be printed out
			//Task - Prints the output preamble
			//Returns - nothing

	//Print out the column header
	output << setw(5)<<"First"; 
	output << setw(14)<<"Last";
	output << setw(23)<<"Test 1";
	output << setw(11)<<"Test 2";
	output << setw(11)<<"Test 3";
	output << setw(11)<<"Test 4";
	if(check==1) //Check to see if the Average and Grade have been calculated, and print if so
	{output << setw(11)<<"Average";
	output << setw(10)<<"Grade";
	}
	output << endl;
	output << setw(6)<<"----------";
	output << setw(11)<<"------";
	output << setw(20)<<"------";
	output << setw(12)<<"------";
	output << setw(11)<<"------";
	output << setw(11)<<"------";
	if(check==1) //Check to see if the Average and Grade have been calculated, and print if so
	{output << setw(11)<<"-------";
	output << setw(11)<<"------";
	}
	output << endl;
	}

