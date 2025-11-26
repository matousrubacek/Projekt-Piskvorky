#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 3
#define STREAK 3
#define BUF 100

//funkce pro inicializaci hraci desky
void initializeBoard(char board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++)
        for (int j = 0; j < SIZE; j++)
            board[i][j] = ' ';
}
//funkce pro vypsani hraci desky
void printBoard(char board[SIZE][SIZE]) {
    printf("\n");
    //vypsani radku desky
    for (int i = 0; i < SIZE; i++) {
        printf(" ");
        for (int j = 0; j < SIZE; j++) {
            printf(" %c ", board[i][j]);
            if (j < SIZE - 1) printf("|");
        }
        //vypsani oddelovace mezi radky
        printf("\n");
        if (i < SIZE - 1) {
            for (int n = 0; n < SIZE; n++) {
                printf("+---");
            }
        }
        printf("\n");
    }
    printf("\n");
}

//funkce pro kontrolu vyhry
int checkWin(char board[SIZE][SIZE], char znak) {
    int nalezeno = 0;
    int win = 0;
    // radky
    for (int i = 0; i < SIZE; ++i) {
        nalezeno = 0;
        win = 0;
        for (int j = 0; j < SIZE; ++j) {
            if (board[i][j] == znak) {
                nalezeno++;
            }
            if (board[i][j] != znak)
            {
                nalezeno = 0;
            }
            if (nalezeno >= STREAK) {
                win = 1;
            }
            if (win == 1) return 1;
        }
    }
    // sloupce
    for (int j = 0; j < SIZE; ++j) {
        nalezeno = 0;
        win = 0;
        for (int i = 0; i < SIZE; ++i) {
            if (board[i][j] == znak) {
                nalezeno++;
            }
            if (board[i][j] != znak)
            {
                nalezeno = 0;
            }
            if (nalezeno >= STREAK) {
                win = 1;
            }
            if (win == 1) return 1;
        }
    }
    // diagonála TL->BR
    win = 0;
    nalezeno = 0;
    for (int i = 0; i < SIZE; ++i) {
        if (board[i][i] == znak) {
            nalezeno++;
        }
        if (board[i][i] != znak)
        {
            nalezeno = 0;
        }
        if (nalezeno >= STREAK) {
            win = 1;
        }
        if (win == 1) return 1;
    }
    // diagonála TR->BL
    win = 0;
    nalezeno = 0;
    for (int i = 0; i < SIZE; ++i) {
        if (board[i][SIZE - 1 - i] == znak) {
            nalezeno++;
        }
        if (board[i][SIZE - 1 - i] != znak)
        {
            nalezeno = 0;
        }
        if (nalezeno >= STREAK) {
            win = 1;
        }
        if (win == 1) return 1;
    }
    return 0;
}
//funkce pro kontrolu, zda je deska plná
int boardFull(char board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; ++i)
        for (int j = 0; j < SIZE; ++j)
            if (board[i][j] == ' ') return 0;
    return 1;
}
//funkce pro zadani tahu 
void promptMove(char board[SIZE][SIZE], char znak) {
    char line[BUF];
    int row = -1, col = -1;
    while (1) {
        printf("Hrac '%c', zadej pozici (radek sloupec, 1..%d): ", znak, SIZE);
        if (!fgets(line, sizeof(line), stdin)) {
            printf("Chyba vstupu. Opakuj.\n");
            continue;
        }
        // odstraneni koncoveho newline
        line[strcspn(line, "\n")] = 0;
        // pwinus parsovat dva cisla
        if (sscanf_s(line, "%d %d", &row, &col) != 2) {
            printf("Neplatny vstup. Zadej dve cisla oddelena mezerou (napr. 1 3).\n");
            continue;
        }
        if (row < 1 || row > SIZE || col < 1 || col > SIZE) {
            printf("Index mimo rozsah. Pouzij cisla 1 az %d.\n", SIZE);
            continue;
        }
        row--; col--; // index od 0
        if (board[row][col] != ' ') {
            printf("Toto pole je jiz obsazene, vyber jine.\n");
            continue;
        }
        board[row][col] = znak;
        break;
    }
}
int main(void) {
    char board[SIZE][SIZE];
    initializeBoard(board);
    char player1 = 'X';
    char player2 = 'O';
    char current = player1;
    printf("Piskvorky \n");
    printf("Hrac 1: '%c', Hrac 2: '%c'\n", player1, player2);
    printf("Pro tah zadejte radek a sloupec (1..%d), napr. '2 3'.\n", SIZE);
    while (1) {
        printBoard(board);
        promptMove(board, current);
        if (checkWin(board, current)) {
            printBoard(board);
            printf("Gratuluji! Hrac '%c' vyhral!\n", current);
            break;
        }
        if (boardFull(board)) {
            printBoard(board);
            printf("Remiza. Zadne volne policka.\n");
            break;
        }
        // prepnout hrace
        if (current == 'X') {
            current = 'O';
        }
        else {
            current = 'X';
        }
    }
    return 0;
}
