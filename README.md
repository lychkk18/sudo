#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <stdbool.h>
#include "board.h"
#include "util.h"

#define SIZE 9

// Function prototypes
void generateSudokuBoard(int board[SIZE][SIZE]);
bool solveSudoku(int board[SIZE][SIZE]);

void generateSudokuBoard(int board[SIZE][SIZE]) {
    // Initialize the board with zeros
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            board[i][j] = 0;
        }
    }

    // Fill the diagonal 3x3 grids
    for (int i = 0; i < SIZE; i += 3) {
        for (int j = 0; j < 3; j++) {
            for (int k = 0; k < 3; k++) {
                int num;
                do {
                    num = (rand() % 9) + 1;
                } while (!isValid(board, i + j, i + k, num));
                board[i + j][i + k] = num;
            }
        }
    }

    // Solve the board to fill it completely
    if (!solveSudoku(board)) {
        printf("Error: Unable to generate a valid Sudoku board.\n");
        return;
    }

    // Remove random cells to create a puzzle
    int cellsToRemove = 40; // Adjust this number for difficulty
    while (cellsToRemove > 0) {
        int row = rand() % SIZE;
        int col = rand() % SIZE;
        if (board[row][col] != 0) {
            board[row][col] = 0;
            cellsToRemove--;
        }
    }
}

bool solveSudoku(int board[SIZE][SIZE]) {
    int row = -1, col = -1;
    bool isEmpty = false;

    // Find an empty cell
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (board[i][j] == 0) {
                row = i;
                col = j;
                isEmpty = true;
                break;
            }
        }
        if (isEmpty) break;
    }

    // If no empty cell is found, the board is solved
    if (!isEmpty) return true;

    // Try numbers 1-9 in the empty cell
    for (int num = 1; num <= 9; num++) {
        if (isValid(board, row, col, num)) {
            board[row][col] = num;

            if (solveSudoku(board)) {
                return true;
            }

            // Backtrack
            board[row][col] = 0;
        }
    }

    return false;
}

int main() {
    int board[SIZE][SIZE];
    int choice;

    srand(time(NULL)); // Seed for random number generation

    generateSudokuBoard(board);

    while (1) {
        printf("\nSudoku Game Menu:\n");
        printf("1. Play Sudoku\n");
        printf("2. Solve Sudoku\n");
        printf("3. Print Board\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: {
                int row, col, num;
                printBoard(board);
                printf("Enter row (1-9), column (1-9), and number (1-9) to place (e.g., 1 1 5): ");
                scanf("%d %d %d", &row, &col, &num);
                row--;
                col--;
                if (row >= 0 && row < SIZE && col >= 0 && col < SIZE && num >= 1 && num <= 9) {
                    if (board[row][col] == 0 && isValid(board, row, col, num)) {
                        board[row][col] = num;
                        if (isComplete(board)) {
                            printf("Congratulations! You completed the Sudoku puzzle!\n");
                            printBoard(board);
                            return 0;
                        }
                    } else {
                        printf("Invalid move. Try again.\n");
                    }
                } else {
                    printf("Invalid input. Try again.\n");
                }
                break;
            }
            case 2:
                if (solveSudoku(board)) {
                    printf("Sudoku solved successfully:\n");
                    printBoard(board);
                } else {
                    printf("No solution exists for the current board.\n");
                }
                break;
            case 3:
                printf("Current Sudoku Board:\n");
                printBoard(board);
                break;
            case 4:
                printf("Exiting the game. Goodbye!\n");
                return 0;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    }

    return 0;
}
