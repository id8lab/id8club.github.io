---
layout: default
title: C++ Project Workshop 1
permalink: cplusplusprojectworkshop1.html
---

# C++ Project Workshop 1

The Project
===========

After brainstorming a number of project ideas, the attending club members voted to select a simple implementation of **Conway's Game of Life**. Another project involving a Socket.IO chat client was a popular choice, but issues regarding library dependencies have unfortunately proven to make that project impractical without holding further workshops on library linkage and use.


Conway's Game of Life
=====================

[Conway's Game of Life](http://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) is a cellular automata that simulates the lives of individual cells on a two-dimensional grid. The game is completely automated, and varies only based on the initial state of the grid. **See the Wikipedia article for the rules of the simulation.**

The aim of this project is to implement the game of life, **using the terminal window for character-based output**. The grid can be drawn by setting the cursor position of the terminal window, and writing whitespace characters for dead/empty cells, and printable characters for live cells.

The following code is provided for students as a starting point (note that the use of `std::initializer_list` requires the code to be compiled in C++11 mode, using **`-std=c++11`**):

```cpp
#include <iostream>
#include <cstdlib>
#include <cstdio>
#include <string>
#include <sstream>
#include <stdexcept>
#include <initializer_list>

#ifdef _WIN32
	#include <windows.h>
#else
	#include <unistd.h>
#endif

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
};

//Grid specialisation for Conways Game of Life
template <int GridWidth, int GridHeight>
class ConwayGrid : public Grid<bool, GridWidth, GridHeight>
{
	private:
		
		//A "null object" cell
		bool nullCell;
		
	public:
		
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
};

int main()
{
	//Create a 10x10 conway grid
	ConwayGrid<10, 10> grid;
	
	//Cells can be accessed using either of these styles:
	grid.get(0,0) = true;  //Using get()
	grid[{0,0}]   = true;  //Using operator[] with a Point instance
	
	//Hide the cursor
	Utility::hideCursor();
	
	//Clear the screen
	Utility::clearScreen();
	
	//Print "1" at (0,0)
	Utility::setCursorPos(0,0);
	std::cout << 1;
	
	//Print "2" at (5,5)
	Utility::setCursorPos(5, 5);
	std::cout << 2;
	
	//Flush the output buffer
	std::cout.flush();
	
	//Block until the user enters some input
	char input;
	std::cin >> input;
	
	return 0;
}
```
