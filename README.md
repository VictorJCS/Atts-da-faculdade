# Atts-da-faculdade|Trabalho|
Atividades da faculdade. 
#include <stdio.h>
#include <stdlib.h>
#include <locale.h>
#include <ctype.h>

#define ROWS 8
#define COLS 8

void printMatrix(int matrix[ROWS][COLS], int itemCount) {
    printf("   ");
    for (int i = 0; i < COLS; i++) {
        printf("%2d ", i);
    }
    printf("\n");

    for (int i = 0; i < ROWS; i++) {
        printf("%2d ", i);
        for (int j = 0; j < COLS; j++) {
            if(matrix[i][j] == 0) {
                printf("|-|");
            } else if (matrix[i][j] == 1) {
                printf("|R|");
            } else {
                printf("|X|");
            }
        }
        printf("\n");
    }
    printf("NÃºmero de itens coletados: %d\n", itemCount);
}

void placeItems(int matrix[ROWS][COLS], int *itemCount, int numItems) {
    int posX, posY;
    for (int i = 0; i < numItems; i++) {
        printf("Digite a posiÃ§Ã£o do item %d (linha coluna): ", i + 1);
        scanf("%d %d", &posX, &posY);

        while(posX < 0 || posX >= ROWS || posY < 0 || posY >= COLS || matrix[posX][posY] != 0) {
            printf("PosiÃ§Ã£o invÃ¡lida para o item %d! Digite novamente: ", i + 1);
            scanf("%d %d", &posX, &posY);
        }

        matrix[posX][posY] = 2;
    }
}

int main() {
    setlocale(LC_ALL, "Portuguese");
    int matrix[ROWS][COLS];
    int numItems, itemCount;
    char playAgain;

    do {
        itemCount = 0;

        for (int i = 0; i < ROWS; i++) {
            for (int j = 0; j < COLS; j++) {
                matrix[i][j] = 0;
            }
        }

        int posX, posY;
        printf("Escolha a posiÃ§Ã£o inicial do robÃ´ (linha coluna): ");
        scanf("%d %d", &posX, &posY);

        while(posX < 0 || posX >= ROWS || posY < 0 || posY >= COLS) {
            printf("PosiÃ§Ã£o inicial invÃ¡lida! Digite novamente: ");
            scanf("%d %d", &posX, &posY);
        }

        matrix[posX][posY] = 1;

        printf("Matriz inicial:\n");
        printMatrix(matrix, itemCount);
        
        printf("Quantos itens vocÃª deseja colocar? ");
        scanf("%d", &numItems);
        placeItems(matrix, &itemCount, numItems);
        
		system("cls");
		
        printf("Matriz com os itens posicionados:\n");
        printMatrix(matrix, itemCount);

        char move;
        printf("\nDigite 'W' para cima, 'A' para esquerda, 'S' para baixo, 'D' para direita\n");
        printf("Digite 'Q' para sair.\n");

        while (itemCount < numItems) {
            scanf(" %c", &move);
            system("cls");

            matrix[posX][posY] = 0;

            switch(tolower(move)) {
                case 'w':
                    if (posX > 0) {
                        posX--;
                    } else {
                        printf("\nMovimento invÃ¡lido! Tente novamente.\n");
                    }
                    break;
                case 'a':
                    if (posY > 0) {
                        posY--;
                    } else {
                        printf("\nMovimento invÃ¡lido! Tente novamente.\n");
                    }
                    break;
                case 's':
                    if (posX < ROWS - 1) {
                        posX++;
                    } else {
                        printf("\nMovimento invÃ¡lido! Tente novamente.\n");
                    }
                    break;
                case 'd':
                    if (posY < COLS - 1) {
                        posY++;
                    } else {
                        printf("\nMovimento invÃ¡lido! Tente novamente.\n");
                    }
                    break;
                case 'q':
                    printf("Saindo...\n");
                    return 0;
                default:
                    printf("\nComando invÃ¡lido! Tente novamente.\n");
            }

            if (matrix[posX][posY] == 2) {
                itemCount++;
            }

            matrix[posX][posY] = 1;

            printMatrix(matrix, itemCount);

            if (itemCount == numItems) {
                printf("\nTodos os itens foram coletados! ParabÃ©ns!\n");
                break;
            }
        }

        printf("\nDeseja jogar novamente? (S/N): ");
        scanf(" %c", &playAgain);
        system("cls");
		
    } while (tolower(playAgain) == 's');

    printf("Saindo...\n");

    return 0;
}
