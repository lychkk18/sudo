#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <stdbool.h>

#define SIZE 9
bool isValid(int board[SIZE][SIZE], int row, int col, int num) {
    for (int x = 0; x < SIZE; x++) {
        if (board[row][x] == num || board[x][col] == num)
            return false;
    }

    int startRow = row - row % 3;
    int startCol = col - col % 3;

    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (board[i + startRow][j + startCol] == num)
                return false;
        }
    }

    return true;
}

bool isComplete(int board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (board[i][j] == 0)
                return false;
        }
    }
    return true;
}

void printBoard(int board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        if (i % 3 == 0 && i != 0)
            printf("---------------------\n");
        for (int j = 0; j < SIZE; j++) {
            if (j % 3 == 0 && j != 0)
                printf("| ");
            printf("%d ", board[i][j] ? board[i][j] : 0);
        }
        printf("\n");
    }
}

bool solveSudoku(int board[SIZE][SIZE]) {
    int row = -1, col = -1;
    bool isEmpty = false;

    for (int i = 0; i < SIZE && !isEmpty; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (board[i][j] == 0) {
                row = i;
                col = j;
                isEmpty = true;
                break;
            }
        }
    }

    if (!isEmpty)
        return true;

    for (int num = 1; num <= 9; num++) {
        if (isValid(board, row, col, num)) {
            board[row][col] = num;
            if (solveSudoku(board))
                return true;
            board[row][col] = 0;
        }
    }

    return false;
}

void fillDiagonalGrids(int board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i += 3) {
        for (int j = 0; j < 3; j++) {
            for (int k = 0; k < 3; k++) {
                int num;
                do {
                    num = rand() % 9 + 1;
                } while (!isValid(board, i + j, i + k, num));
                board[i + j][i + k] = num;
            }
        }
    }
}

void generateSudokuBoard(int board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++)
        for (int j = 0; j < SIZE; j++)
            board[i][j] = 0;

    fillDiagonalGrids(board);

    if (!solveSudoku(board)) {
        printf("Error: Unable to generate a valid Sudoku board.\n");
        return;
    }

    int cellsToRemove = 40;
    while (cellsToRemove > 0) {
        int row = rand() % SIZE;
        int col = rand() % SIZE;
        if (board[row][col] != 0) {
            board[row][col] = 0;
            cellsToRemove--;
        }
    }
}

int main() {
    int board[SIZE][SIZE];
    int choice;

    srand(time(NULL));
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
                printf("Enter row (1-9), column (1-9), and number (1-9): ");
                scanf("%d %d %d", &row, &col, &num);
                row--; col--;

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
