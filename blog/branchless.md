# Code Snips

```
#include<iostream>
#include <thread>
#include <chrono>
#include <stdlib.h>     /* srand, rand */

const int ROWS = 64;
const int COLS = 16;
const int ITERS = 10000;

bool board[COLS][ROWS] = {};
int neighbors[COLS][ROWS] = {};
const bool START[COLS][ROWS] = {
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
    0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
};

void initBoard()
{
    // some seed
    srand(25);
    // set active board to starting board
    for (int i = 0; i < COLS; ++i)
    {
        for (int j = 0; j < ROWS; ++j)
        {
            board[i][j] = START[i][j];
        }
    } 
}

void printBoard()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            std::cout << (board[c][r] ? "X" : ".");
        }
        std::cout << std::endl;
    }
    std::cout << std::endl;
    std::cout << std::endl;
}

void printNeighbors()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            std::cout << (neighbors[c][r]);
        }
        std::cout << std::endl;
    }
    std::cout << std::endl;
    std::cout << std::endl;
}

void zeroNeighbors()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            neighbors[c][r] = 0;
        }
    }
}

unsigned int modulo( int value, unsigned m) {
    int mod = value % (int)m;
    if (mod < 0) {
        mod += m;
    }
    return mod;
}

void calcNeighborsSingleBranch()
{
    zeroNeighbors();
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            if (board[c][r])
            {
                int minC = modulo(c-1, COLS);
                int maxC = modulo(c+1, COLS);
                int minR = modulo(r-1, ROWS);
                int maxR = modulo(r+1, ROWS);

                // prev row
                ++neighbors[minC][minR];
                ++neighbors[c][minR];
                ++neighbors[maxC][minR];

                // our row
                ++neighbors[minC][r];
                ++neighbors[maxC][r];

                // nex row
                ++neighbors[minC][maxR];
                ++neighbors[c][maxR];
                ++neighbors[maxC][maxR];
            }
        }
    }
}

void calcNeighborsBetterMods()
{
    zeroNeighbors();
    for (int c = 0; c < COLS ; ++c)
    {
        int minC = modulo(c-1, COLS);
        int maxC = modulo(c+1, COLS);

        for (int r = 0; r < ROWS; ++r)
        {
            if (board[c][r])
            {        
                int minR = modulo(r-1, ROWS);
                int maxR = modulo(r+1, ROWS);

                // prev row
                ++neighbors[minC][minR];
                ++neighbors[c][minR];
                ++neighbors[maxC][minR];

                // our row
                ++neighbors[minC][r];
                ++neighbors[maxC][r];

                // nex row
                ++neighbors[minC][maxR];
                ++neighbors[c][maxR];
                ++neighbors[maxC][maxR];
            }
        }
    }
}

void calcNeighborsBestMods()
{
    zeroNeighbors();
    for (int c = 0; c < COLS ; ++c)
    {
        int minC = (c + COLS-1 ) % COLS;
        int maxC = (c + COLS+1 )% COLS;

        for (int r = 0; r < ROWS; ++r)
        {
            if (board[c][r])
            {        
                int minR = (r + ROWS - 1) % ROWS;
                int maxR = (r + ROWS + 1) % ROWS;

                // prev row
                ++neighbors[minC][minR];
                ++neighbors[c][minR];
                ++neighbors[maxC][minR];

                // our row
                ++neighbors[minC][r];
                ++neighbors[maxC][r];

                // nex row
                ++neighbors[minC][maxR];
                ++neighbors[c][maxR];
                ++neighbors[maxC][maxR];
            }
        }
    }
}

void calcNeighborsBranchless()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            int minC = modulo(c-1, COLS);
            int maxC = modulo(c+1, COLS);
            int minR = modulo(r-1, ROWS);
            int maxR = modulo(r+1, ROWS);

            int& thisCell = neighbors[c][r];
            thisCell = 0;

            // prev row
            thisCell += (board[minC][minR]);
            thisCell += (board[c][minR]);
            thisCell += (board[maxC][minR]);

            // our row
            thisCell += (board[minC][r]);
            thisCell += (board[maxC][r]);

            // nex row
            thisCell += (board[minC][maxR]);
            thisCell += (board[c][maxR]);
            thisCell += (board[maxC][maxR]);
        }
    }
}

void calcNeighborsBranchless2()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            int minC = modulo(c-1, COLS);
            int maxC = modulo(c+1, COLS);
            int minR = modulo(r-1, ROWS);
            int maxR = modulo(r+1, ROWS);

            int& thisCell = neighbors[c][r];
            thisCell = 
                (board[minC][minR]) + 
                (board[c][minR]) + 
                (board[maxC][minR]) + 
                (board[minC][r]) + 
                (board[maxC][r]) + 
                (board[minC][maxR]) + 
                (board[c][maxR]) + 
                (board[maxC][maxR]);
        }
    }
}

void calcNeighborsBranchy()
{
    for (int c = 0; c < COLS ; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            int minC = modulo(c-1, COLS);
            int maxC = modulo(c+1, COLS);
            int minR = modulo(r-1, ROWS);
            int maxR = modulo(r+1, ROWS);

            int& thisCell = neighbors[c][r];

            // prev row
            if(board[minC][minR]) ++thisCell;
            if(board[c][minR]) ++thisCell;
            if(board[maxC][minR]) ++thisCell;

            // our row
            if(board[minC][r]) ++thisCell;
            if(board[maxC][r]) ++thisCell;

            // nex row
            if(board[minC][maxR]) ++thisCell;
            if(board[c][maxR]) ++thisCell;
            if(board[maxC][maxR]) ++thisCell;
        }
    }
}

void updateBoardV1()
{
     // set active board to starting board
    for (int c = 0; c < COLS; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            bool isAlive = board[c][r];
            int numNeighbors = neighbors[c][r];

            //Any live cell with two or three live neighbours survives.
            //Any dead cell with three live neighbours becomes a live cell.
            //board[c][r] = isAlive 
            //   ? (numNeighbors > 1 && numNeighbors < 3) 
            //    : numNeighbors == 3;
            if (isAlive)
            {
                board[c][r] = numNeighbors == 2 || numNeighbors == 3;
            }
            else
            {
                board[c][r] = numNeighbors == 3;
            }
            
        }
    }
}

void updateBoardV2()
{
     // set active board to starting board
    for (int c = 0; c < COLS; ++c)
    {
        for (int r = 0; r < ROWS; ++r)
        {
            bool isAlive = board[c][r];
            int numNeighbors = neighbors[c][r];
            board[c][r] = numNeighbors == 3 || (isAlive && numNeighbors == 2);
        }
    }
}

void sleep()
{
    std::this_thread::sleep_for(std::chrono::milliseconds(10));
}

void test1()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsSingleBranch();
        updateBoardV1();
        //printBoard();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 1 time in microseconds " << duration << std::endl;
}

void test2()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsBranchy();
        updateBoardV2();
        //printBoard();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 2 time in microseconds " << duration << std::endl;
}   

void test3()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsBranchless();
        updateBoardV1();
        //printNeighbors();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 3 time in microseconds " << duration << std::endl;
}   

void test4()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsBranchless2();
        updateBoardV1();
        //printNeighbors();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 4 time in microseconds " << duration << std::endl;
}   

void test5()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsBetterMods();
        updateBoardV1();
        //printBoard();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 5 time in microseconds " << duration << std::endl;
}

void test6()
{
    initBoard();
    auto t1 = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERS; ++i)
    {
        calcNeighborsBestMods();
        updateBoardV1();
        //printBoard();
        //sleep();
    }
    auto t2 = std::chrono::high_resolution_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::microseconds>( t2 - t1 ).count();
    std::cout << "test 6 time in microseconds " << duration << std::endl;
}

int main() {
    std::cout << "Game Of Life\n";

    test1();
    test2();
    test3();
    test4();
    test5();
    test6();

    return 0;
}

## Build Script
```
g++ -std=c++11 -o main main.cpp && ./main
```

```
## Results
```
$ ./build_and_run.sh 

Game Of Life
test 1 time in microseconds 90375
test 2 time in microseconds 253793
test 3 time in microseconds 305055
test 4 time in microseconds 265710
test 5 time in microseconds 89726
test 6 time in microseconds 86706

$ ./build_and_run.sh 
Game Of Life
test 1 time in microseconds 89917
test 2 time in microseconds 255202
test 3 time in microseconds 298298
test 4 time in microseconds 264149
test 5 time in microseconds 89364
test 6 time in microseconds 86223

$ ./build_and_run.sh 
Game Of Life
test 1 time in microseconds 91940
test 2 time in microseconds 254379
test 3 time in microseconds 296202
test 4 time in microseconds 267663
test 5 time in microseconds 88878
test 6 time in microseconds 87090
```
