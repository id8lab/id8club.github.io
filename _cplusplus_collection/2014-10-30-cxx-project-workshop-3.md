---
layout: default
title: C++ Workshop 3
description: "Accessing command-line arguments in C++"
permalink: cplusplusworkshop3.html
---

# C++ Project Workshop 3

Accessing command-line arguments in C++
=======================================

Command-line arguments can be accessed in C++ by adding two arguments to the `main` function: `argc` and `argv`, like so:

```cpp
//argc -> argument count
//argv -> argument values
int main (int argc, char* argv[])
{
	//Do stuff with arguments...
	
	return 0;
}
```

`argv` is equivalent to to `String[] args` from the `main` method in Java. It is an array of C-style strings (null-terminated character arrays.) Since arrays in C and C++ do not store their length, the argument `argc` provides the length of the array.


CSV Generator for Conway's Game of Life
=======================================

To provide an easy way to create the starting state for Conway's Game of Life, we've created a simple web-based tool, [which can be found here](../live/conway/gencsv.html). Simply fill in the cells that you want to start in the 'live' state, generate the CSV data, and save it to a file. The CSV file can then be passed to the completed version of Conway's Game of Life, provided below.


Completed Code for Conway's Game of Life
========================================

Below is an example of the completed implementation for our simple command-line based Conway's Game of Life. The code can be compiled like so (under MinGW, use `conway.exe` for the output filename instead of `conway`, under Mac OS X, use `clang++` as the compiler instead of `g++`):

```
g++ -std=c++11 -o conway conway.cpp
```

The application accepts two optional command-line arguments:

- Input CSV file for the starting grid state
- Framerate (specified in FPS)

For example, assume we have a CSV file named `start.csv`. To run the game using this CSV file, at 15fps, use the following command (under MinGW, use `conway.exe` instead of `./conway`):

```
./conway start.csv 15
```

Note that if no framerate is specified, the game defaults to 15fps. If no CSV file is specified, a random starting grid state is generated.

The completed code is as follows:

```cpp
#include <fstream>
#include <iostream>
#include <cstdlib>
#include <ctime>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#include <vector>
#include <sstream>
#include <stdexcept>
#include <initializer_list>

#ifdef _WIN32
	#include <windows.h>
#else
	#include <unistd.h>
#endif

using std::vector;
using std::string;
using std::stringstream;

//Utility functionality
class Utility
{
	public:
		
		//Uses a stringstream to generate a textual representation of a value
		template <typename T>
		static string toString(const T& i)
		{
			stringstream buf;
			buf << i;
			return buf.str();
		}
		
		//Uses stringstream functionality for typecasting
		template <typename O, typename I>
		static O to (const I& input)
		{
			//Create a stringstream
			stringstream buf;
			O output;
			
			//Input the argument and extract it as the specified type
			buf << input;
			buf >> output;
			
			//Return the extracted data
			return output;
		}
		
		//Uses stringstream functionality for type validation
		template <typename TestFor, typename T>
		static bool is (const T& i)
		{
			//Create a stringstream
			stringstream buf;
			TestFor test;
			
			//Input the argument and try to extract as the specified type
			buf << i;
			buf >> test;
			
			//Return whether or not the extraction succeeded
			return (!buf.fail());
		}
		
		//Uses platform-specific CLI functionality to set the cursor position of stdout and stderr
		static void setCursorPos(int x, int y, bool stdOut = true, bool stdErr = true)
		{
			#ifdef _WIN32
				
				//As usual, Windows just has to be different to everyone else
				HANDLE hStdOut = GetStdHandle(STD_OUTPUT_HANDLE);
				HANDLE hStdErr = GetStdHandle(STD_ERROR_HANDLE);
				COORD pos;
				pos.X = x;
				pos.Y = y;
				if (stdOut) { SetConsoleCursorPosition(hStdOut, pos); }
				if (stdErr) { SetConsoleCursorPosition(hStdErr, pos); }
				
			#else
				
				//Good old escape sequences
				if (stdOut) { fprintf(stdout, "\x1B[%u;%uH", y, x); }
				if (stdErr) { fprintf(stderr, "\x1B[%u;%uH", y, x); }
				
			#endif
		}
		
		//Uses platform-specific CLI functionality to hide the cursor
		static void hideCursor(bool stdOut = true, bool stdErr = true)
		{
			#ifdef _WIN32
				
				HANDLE hStdOut = GetStdHandle(STD_OUTPUT_HANDLE);
				HANDLE hStdErr = GetStdHandle(STD_ERROR_HANDLE);
				CONSOLE_CURSOR_INFO cursor;
				cursor.dwSize = 1;
				cursor.bVisible = 0;
				if (stdOut) { SetConsoleCursorInfo(hStdOut, &cursor); }
				if (stdErr) { SetConsoleCursorInfo(hStdErr, &cursor); }
				
			#else
				
				if (stdOut) { fprintf(stdout, "\x1b[?25l"); }
				if (stdErr) { fprintf(stderr, "\x1b[?25l"); }
				
			#endif
		}
		
		//Uses the platform-specific command to clear the console window
		static void clearScreen()
		{
			#ifdef _WIN32
				system("cls");
			#else
				system("clear");
			#endif
		}
		
		//Suspends program execution for the specified number of seconds
		static void sleep(double seconds)
		{
			#ifdef _WIN32
				Sleep( (DWORD)(seconds * 1000.0) );
			#else
				usleep( (useconds_t)(seconds * 1000000.0) );
			#endif
		}
		
		//Replaces all occurences of a substring with the specified replacement
		static string stringReplace(const string& find, const string& replace, string subject)
		{
			size_t uPos        = 0;
			size_t uFindLen    = find.length();
			size_t uReplaceLen = replace.length();
			
			//If looking for an empty string, simply return the subject
			if (uFindLen == 0) {
				return subject;
			}
			
			//Loop through and find all instances
			while ((uPos = subject.find(find, uPos)) != string::npos)
			{
				subject.replace(uPos, uFindLen, replace);
				uPos += uReplaceLen;
			}
			
			//Return the modified string
			return subject;
		}
		
		//Splits a string into a list of substrings based on the specified delimiter
		static vector<string> explode(const string& delim, const string& s, size_t limit = 0)
		{
			//Counters for the current and previous match positions
			size_t prevPos      = 0;
			size_t currPos      = 0;
			
			//Keep count of the number of substrings we have captured
			size_t captureCount = 0;
			
			//Keep track of the length of the delimiter itself so it can be excluded from the results
			size_t uDelimLen    = delim.length();
			
			//A vector of strings to hold the results
			vector<string> result;
			
			//If looking for an empty string, simply return a vector containing the subject
			if (uDelimLen == 0)
			{
				result.push_back(s);
				return result;
			}
			
			//Loop through and find all instances of the delimiter
			while (((currPos = s.find(delim, currPos)) != string::npos) && (!limit || captureCount < limit-1))
			{
				//Add the intervening substring to the vector
				result.push_back(s.substr(prevPos, currPos - prevPos));
				
				//Increment the captured substring count
				captureCount++;
				
				//Skip over the delimiter and copy to prevPos
				currPos += uDelimLen;
				prevPos = currPos;
			}
			
			//Retrieve the remaining characters (if any)
			currPos = s.length();
			if (prevPos <= currPos) {
				result.push_back(s.substr(prevPos, currPos - prevPos));
			}
			
			//Return the list of substrings
			return result;
		}
		
		//Retrieves the contents of a file
		static string getFileContents(const string& filename)
		{
			string contents = "";
			
			//Attempt to open the specified input file
			std::ifstream infile(filename.c_str(), std::ios::binary);
			if (infile.is_open())
			{
				//Utilise a fixed-size buffer for reading the file in chunks
				char buffer[1024*1024];
				size_t bytesRead = 0;
				while ( (bytesRead = infile.read(buffer, sizeof(buffer)).gcount()) != 0 ) {
					contents.append(buffer, bytesRead);
				}
				
				//Close the input file
				infile.close();
			}
			
			return contents;
		}
};

//Represents a point in 2D space, as an (x,y) coordinate
class Point
{
	public:
		
		Point(int x, int y)
		{
			this->x = x;
			this->y = y;
		}
		
		//Allows for using initialiser lists, like so:
		//Point p = {0,0};
		Point(const std::initializer_list<int>& vals)
		{
			this->x = *(vals.begin());
			this->y = *(vals.begin() + 1);
		}
		
		int x;
		int y;
};

//Represents a generic 2D grid of cells
template <typename CellTy, int GridWidth, int GridHeight>
class Grid
{
	protected:
		
		//The array of cells
		CellTy cells[ GridWidth * GridHeight ];
		
		//(x,y) -> array index
		int resolveCoordinates(int x, int y) {
			return (y * GridWidth) + x;
		}
		
	public:
		
		//Cell accessor
		virtual CellTy& get(int x, int y)
		{
			int index = this->resolveCoordinates(x, y);
			
			//Check that the specified index is within the array bounds
			if (index < 0 || index >= GridWidth * GridHeight) {
				throw std::out_of_range("Coordinates [" + Utility::toString(x) + ", " + Utility::toString(y) + "]");
			}
			
			return cells[index];
		}
		
		//Overloaded square-brackets operator
		virtual CellTy& operator[](const Point& p) {
			return this->get(p.x, p.y);
		}
		
		//Fills the grid cells with the specified value
		void fill(const CellTy& val)
		{
			for (int index = 0; index < (GridWidth * GridHeight); ++index) {
				cells[index] = val;
			}
		}
		
		//Populates grid cells with the contents of a vector
		void populate(const vector<CellTy>& vals)
		{
			//Process either the number of supplied values or the grid size, whichever is smaller
			int valsToUse = std::min((int)(vals.size()), GridWidth * GridHeight);
			for (int index = 0; index < valsToUse; ++index) {
				cells[index] = vals[index];
			}
		}
};

//Grid specialisation for Conways Game of Life
template <int GridWidth, int GridHeight>
class ConwayGrid : public Grid<bool, GridWidth, GridHeight>
{
	protected:
		
		//A "null object" cell
		bool nullCell;
		
		//Performs a neighbour query for a cell
		int neighbourQuery(int x, int y)
		{
			vector<bool> neighbourVals;
			neighbourVals.push_back( this->operator[]({ x - 1, y - 1 }) );
			neighbourVals.push_back( this->operator[]({ x,     y - 1 }) );
			neighbourVals.push_back( this->operator[]({ x + 1, y - 1 }) );
			neighbourVals.push_back( this->operator[]({ x - 1, y     }) );
			neighbourVals.push_back( this->operator[]({ x + 1, y     }) );
			neighbourVals.push_back( this->operator[]({ x - 1, y + 1 }) );
			neighbourVals.push_back( this->operator[]({ x,     y + 1 }) );
			neighbourVals.push_back( this->operator[]({ x + 1, y + 1 }) );
			return std::count(neighbourVals.begin(), neighbourVals.end(), true);
		}
		
	public:
		
		ConwayGrid(bool randomVals = false)
		{
			//Determine if a randomised grid has been requested
			if (randomVals == true)
			{
				//Generate a random value for each cell
				for (int cellIndex = 0; cellIndex < (GridWidth * GridHeight); ++cellIndex) {
					this->cells[cellIndex] = (rand() % 2 != 0);
				}
			}
			else
			{
				//Default to an empty grid
				memset(this->cells, false, GridWidth * GridHeight);
			}
		}
		
		//Overrides base class version to provide special behaviour for invalid cell queries
		bool& get(int x, int y)
		{
			//Attempt to retrieve the specified cell
			try {
				return Grid<bool, GridWidth, GridHeight>::get(x, y);
			}
			catch (std::out_of_range& e)
			{
				//For invalid cell queries, return a "null object" cell with value false
				this->nullCell = false;
				return this->nullCell;
			}
		}
		
		//Resets the grid to an empty state
		void clearGrid() {
			this->fill(false);
		}
		
		//Advances the game state by one "generation"
		void advanceState()
		{
			//Create a temporary array to hold the new generation, and copy the current values
			//This is done to prevent updated cell values from effecting neighbourhood queries
			bool newCells[ GridWidth * GridHeight ];
			memcpy(newCells, this->cells, GridWidth * GridHeight);
			
			//Iterate over the columns of the grid
			for (int x = 0; x < GridWidth; ++x)
			{
				//Iterate over the
				for (int y = 0; y < GridHeight; ++y)
				{
					//Bind a ref to the current cell and query its neighbours
					bool& currCell = newCells[ this->resolveCoordinates(x, y) ];
					int liveNeighbours = this->neighbourQuery(x, y);
					
					//Determine the new cell state
					if (liveNeighbours < 2 || liveNeighbours > 3)
					{
						//Cell dies due to under- or over-population
						currCell = false;
					}
					else if (currCell == true && (liveNeighbours == 2 || liveNeighbours == 3)) {
						//Cell continues to live
					}
					else if (currCell == false && liveNeighbours == 3)
					{
						//Dead cell becomes alive due to reproduction
						currCell = true;
					}
				}
			}
			
			//Overwrite the old array values with the new generation
			memcpy(this->cells, newCells, GridWidth * GridHeight);
		}
};

class ConwayRenderer
{
	public:

		template <int GridWidth, int GridHeight>
		static void renderGrid(ConwayGrid<GridWidth, GridHeight>& grid)
		{
			for (int x = 0; x < GridWidth; ++x)
			{
				for (int y = 0; y < GridHeight; ++y)
				{
					//Retrieve the current cell
					bool currCell = grid[{x,y}];
					
					//Render the current cell
					Utility::setCursorPos(x, y, true, false);
					std::cout << ((currCell == true) ? '#' : ' ');
				}
			}
			
			//Flush the output buffer
			std::cout.flush();
		}
};

class CsvReader
{
	public:

		template <typename T>
		static vector<T> readCsvFile(const string& filename, unsigned int targetCols, unsigned int targetRows)
		{
			//The values in the CSV file will be interpreted as type T
			vector<T> values;
			
			//Read the contents of the CSV file
			string contents = Utility::getFileContents(filename);
			
			//Make line endings consistent
			contents = Utility::stringReplace("\r\n", "\n", contents);
			contents = Utility::stringReplace("\n\n", "\n", contents);
			contents = Utility::stringReplace("\r",   "",   contents);
			
			//Remove all whitespace
			contents = Utility::stringReplace(" ",  "", contents);
			contents = Utility::stringReplace("\t", "", contents);
			
			//Iterate over each line in the CSV data
			vector<string> lines = Utility::explode("\n", contents);
			
			unsigned int lineNum = 1;
			for (auto currLine : lines)
			{
				//Ignore any rows outside of the target grid size
				if (lineNum > targetRows) {
					break;
				}
				
				//Ignore any empty lines
				if (currLine.length() == 0) {
					break;
				}
				
				//Iterate over each of the values in the line
				vector<string> stringValues = Utility::explode(",", currLine);
				int colNum = 1;
				for (auto currVal : stringValues)
				{
					//Ignore any columns outside of the target grid size
					if (colNum > targetCols) {
						break;
					}
					
					//Convert the string value to the desired datatype
					if (Utility::is<T>(currVal)) {
						values.push_back( Utility::to<T>(currVal) );
					}
					else {
						values.push_back(T());
					}
					
					colNum++;
				}
				
				lineNum++;
			}
			
			return values;
		}
};

#define GRID_WIDTH  80
#define GRID_HEIGHT 24

#define DEFAULT_FPS 15.0

int main(int argc, char* argv[])
{
	//Hide the console cursor and clear the screen when the application starts
	Utility::hideCursor();
	Utility::clearScreen();
	
	//Seed the random number generator
	srand(time(NULL));
	
	//Create a randomised conway grid
	ConwayGrid<GRID_WIDTH, GRID_HEIGHT> grid(true);
	
	//Check if a seed file was supplied
	if (argc > 1)
	{
		//Read the CSV values
		vector<bool> values = CsvReader::readCsvFile<bool>(argv[1], GRID_WIDTH, GRID_HEIGHT);
		
		//If reading the CSV file succeeded, at least one value will have been parsed
		if (!values.empty())
		{
			//Reset the grid to a blank state
			grid.clearGrid();
			
			//Populate the grid with the values from the CSV file
			grid.populate(values);
		}
		else
		{
			//Failed to read values from the specified CSV file
			std::clog << "Invalid seed CSV file!" << std::endl;
		}
	}
	
	//If the user specified a framerate, use that instead of the default of 15fps
	double framerate = 1.0 / DEFAULT_FPS;
	if (argc > 2 && Utility::is<double>(string(argv[2]))) {
		framerate = 1.0 / Utility::to<double>(string(argv[2]));
	}
	
	//The main render loop
	while(1)
	{
		//Render the current state, and then advance the game state
		ConwayRenderer::renderGrid(grid);
		grid.advanceState();
		
		//Control the framerate by suspending execution after each frame
		Utility::sleep(framerate);
	}
	
	return 0;
}
```
