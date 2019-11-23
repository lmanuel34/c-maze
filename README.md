# c++-maze

this project reads in a .txt file that is a maze and the program finds a path to the goal. 

//sample code

#include "Maze.h"
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
	
Maze::Maze() : mazeWidth(0), mazeLength(0), exitRow(0), exitCol(0), entRow(0), entCol(0)
{
	SquareData* mySquare = new SquareData;
	mySquare->Row = 0;
	mySquare->Col = 0;
	mySquare->State = ' ';
	squares.push_back(mySquare);
}

Maze::Maze(std::ifstream& inputFile) {
	cout << "State of file reads: " << endl;
	cout << "Good: " << inputFile.good() << endl;
	cout << "eof: " << inputFile.eof() << endl;
	cout << "failbit: " << inputFile.fail() << endl;
	cout << "badbit: " << inputFile.bad() << endl << endl;
	// initialize maze constants
	if (!inputFile.good())
	{
		cout << "File not Accepted." << endl;
	}
	else {
		cout << "File Accepted. Initializing maze constants: " << endl << endl;
		inputFile >> mazeWidth >> mazeLength >> exitRow >> exitCol >> entRow >> entCol;
		cout << "MazeWidth: " << mazeWidth << endl;
		cout << "MazeLength: " << mazeLength << endl;
		cout << "exitRow: " << exitRow << endl;
		cout << "exitCol: " << exitCol << endl;
		cout << "entRow: " << entRow << endl;
		cout << "entCol: " << entCol << endl << endl;
	}
	string temp;
	// burn the next line
	getline(inputFile, temp);
	//initialize read
	int rowCounter = 0;
	int tempStringLength = 0;
	while (inputFile.good())
	{
		// get the file line
		string temp;
		getline(inputFile, temp);
		tempStringLength = temp.length();
		cout << "String reads: " << temp << endl;
		int colCounter = 0;
		int squareCounter = 0;
		SquareData* mySquare = nullptr;
		//create squares for all columns and push onto the vector representing the maze
		for (int loopVar = 0; loopVar < tempStringLength; loopVar++)
		{
			// create a square
			mySquare = new SquareData;
			// initialize state of square
			mySquare->setState(temp[loopVar]);
			// initialize row of square
			mySquare->setRow(rowCounter);
			// initialize col of square
			mySquare->setCol(loopVar);
			// push the struct onto the vector
			squares.push_back(mySquare);
			colCounter++;
			if (squareCounter == 0)
			{
				cout << "first Square has the following properties: " << endl;
				cout << "Square State: " << temp[loopVar] << " or " << mySquare->State << endl;
				cout << "Square Row: " << mySquare->Row << endl;
				cout << "Square Col: " << mySquare->Col << endl;
			}
			squareCounter += 1;
		}
		if (tempStringLength != mazeWidth)
		{
			for (int loopVar = tempStringLength; loopVar < mazeWidth; loopVar++)
			{
				// create a square
				SquareData* mySquare = new SquareData;
				// initialize state of Square
				mySquare->setState(clearMarker);
				//initialize row of square
				mySquare->setRow(rowCounter);
				// initialize col of square
				mySquare->setCol(loopVar);
				//push the struct onto the vector
				squares.push_back(mySquare);
			}
		}
		// increment the rowCounter
		rowCounter++;
	}
	cout << "Maze construction should be complete." << endl << endl;
	//give the creature the mazePtr
	Creature::setMaze(this);
}
Maze::Maze(const Maze& myMaze) {

	mazeLength = myMaze.mazeLength;
	mazeWidth = myMaze.mazeWidth;
	exitRow = myMaze.exitRow;
	exitCol = myMaze.exitCol;
	entRow = myMaze.entRow;
	entCol = myMaze.entCol;

	for (int loopVar = 0; loopVar < mazeLength*mazeWidth; loopVar++)
	{
		cout << "Copying vector with index " << loopVar << endl;
		squares.push_back(new SquareData(*myMaze.squares[loopVar]));
	}
	cout << "Ending Copy" << endl;
}
void Maze::vectorDisplay() const {
	for (int loopVar = 0; loopVar < squares.size(); loopVar++)
	{
		cout << "Vector Index: " << loopVar << endl;
		cout << "Pointer: " << squares[loopVar] << endl;
		cout << "Row: " << squares[loopVar]->Row << endl;
		cout << "Col: " << squares[loopVar]->Col << endl;
		cout << "State: " << squares[loopVar]->State << endl;
	}
} //end vectorDisplay
Maze::~Maze() {
	//clear the maze of contents (vector is full of pointers whose memory is on the heap)
	clear();
	//vector safe to deallocate itself now
	Creature::setMaze(nullptr); // remove the pointer from creature
}
// clears maze of its contents
void Maze::clear() {
	int totalSquares = squares.size();
	for (int loopVar = 0; loopVar < totalSquares; loopVar++)
	{
		delete squares[loopVar]; // deallocate memory by deleting the square structure
		squares[loopVar] = nullptr; // eliminate dangling pointer
	} // vector takes care of itself
} // end clear

  // displays the maze
void Maze::display() const {
	cout << "Displaying the maze: " << endl;
	int rowCounter = 0;
	int totalSquares = squares.size();
	for (int loopVar = 0; loopVar < totalSquares; loopVar++)
	{
		if (squares[loopVar]->Row != rowCounter)
		{
			cout << endl;
			cout << squares[loopVar]->State;
			rowCounter++;
		}
		else cout << squares[loopVar]->State;
	}
	cout << endl << endl;
} // end display
  //** CONSTANT ACCESSORS */
int Maze::getEntCol() const {
	return entCol;
}
int Maze::getEntRow() const {
	return entRow;
}
int Maze::getExitCol() const {
	return exitCol;
}
int Maze::getExitRow() const {
	return exitRow;
}
int Maze::getMazeLength() const {
	return mazeLength;
}
int Maze::getMazeWidth() const {
	return mazeWidth;
}
char Maze::getClearSymbol() const {
	return clearMarker;
}

char Maze::getGoal() const {
	return goal;
}
char Maze::getPathSymbol() const {
	return pathMarker;
}
//** SQUARE ACCESSOR METHODS */
SquareData* Maze::getSquare(const int& squareRow, const int& squareCol) const
{
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	return squares[squareIndex];
}

char Maze::getSquareState(const int& squareRow, const int& squareCol) const {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	cout << "SquareIndex: " << squareIndex << endl;
	cout << "squares[squareIndex]: " << squares[squareIndex] << endl;
	return squares[squareIndex]->State;
}
bool Maze::isClear(const int& squareRow, const int& squareCol) const {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	cout << "Index of square is: " << squareIndex << endl;
	cout << "State of Square is: " << squares[squareIndex]->State << endl;
	return squares[squareIndex]->State == ' ';
}
bool Maze::isOnPath(const int& squareRow, const int& squareCol) const {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	return squares[squareIndex]->State == pathMarker;
}
bool Maze::isWall(const int& squareRow, const int& squareCol) const {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	cout << "Index of square is: " << squareIndex << endl;
	return squares[squareIndex]->State == wallMarker;	
}
bool Maze::visited(const int& squareRow, const int& squareCol) const {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	return squares[squareIndex]->State == visitMarker;
}
bool Maze::markSquareAsPath(const int& squareRow, const int& squareCol) {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	squares[squareIndex]->State = pathMarker;
	return true;
}
bool Maze::markSquareAsVisited(const int& squareRow, const int& squareCol) {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	if (isWall(squareRow, squareCol))
	{
		return false;
	}
	squares[squareIndex]->State = visitMarker;
	return true;
}
bool Maze::markSquareAsClear(const int& squareRow, const int& squareCol) {
	int squareIndex = getIndexOfSquare(squareRow, squareCol);
	squares[squareIndex]->State = clearMarker;
	return true;
}

int Maze::numSquares() const {
	return squares.size();
}
int Maze::getIndexOfSquare(const int& squareRow, const int& squareCol) const {
	bool indexFound = false;
	int numSquare = numSquares(); // number of squares in maze
	int loopVar = 0;
	while (!indexFound && loopVar < numSquare)
	{
		if (squares[loopVar]->Row == squareRow && squares[loopVar]->Col == squareCol)
		{
			indexFound = true;
		}
		else loopVar++;
	}
	return loopVar;
}

