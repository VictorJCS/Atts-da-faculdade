//Trabalho versão final: Robo de limpeza.
#include <stdio.h>//Coração
#include <stdlib.h>//Extensão do dio
#include <ctype.h>//Reseta a tela
#include <locale.h>//Escolher idioma
#include <windows.h>//Para usar os comandos do cmd

// (#Define) uma variável (global) = pode ser usada em todo código
#define ROWS 8 // Define o número de linhas
#define COLS 8 // Define o número de colunas

// Função para imprimir a matriz representando o ambiente
void printMatrix(int matrix[ROWS][COLS], int itemCount, int posX, int posY, int initialPosX, int initialPosY) {
    // Imprime os números das colunas
    printf("   "); // Imprimir vazio
    for (int i = 0; i < COLS; i++) // Números da coluna + contador
        printf("%2d ", i); // Imprime o número da coluna
    printf("\n"); // Quebra de linha

    // Imprime a matriz
    for (int i = 0; i < ROWS; i++) { // Números da linha + contador
        printf("%2d ", i); // Imprime o número da linha
        for (int j = 0; j < COLS; j++) {
            // Verifica se a posição é a do robô, a posição inicial vazia ou outra célula
            if (i == posX && j == posY)
                printf("|R|"); // Robô
            else if (i == initialPosX && j == initialPosY && matrix[i][j] != 1)
                printf("|E|"); // Posição inicial vazia
            else
                printf("|%c|", matrix[i][j] == 0 ? '-' : (matrix[i][j] == 1 ? 'R' : (matrix[i][j] == 2 ? 'S' : '-'))); // Outras células
        }
        printf("\n");
    }
}

// Verifica se há sujeira nas células vizinhas à posição atual do robô
int hasDirtNearby(int matrix[ROWS][COLS], int posX, int posY) {
    // Percorre as células vizinhas nas direções horizontal e vertical
    for (int i = -1; i <= 1; i++) {
        for (int j = -1; j <= 1; j++) {
            // Exclui a verificação para células diagonais
            if ((i == 0 || j == 0) && (i != 0 || j != 0)) {
                int nextX = posX + i;
                int nextY = posY + j;
                // Verifica se a célula vizinha está dentro dos limites da matriz e se contém sujeira
                if (nextX >= 0 && nextX < ROWS && nextY >= 0 && nextY < COLS && matrix[nextX][nextY] == 2)
                    return 1; // Retorna verdadeiro se houver sujeira próxima
            }
        }
    }
    return 0; // Retorna falso se não houver sujeira próxima
}

// Coloca as sujeiras aleatoriamente na matriz
void placeDirt(int matrix[ROWS][COLS], int *itemCount, int numItems) { // Puxa a Matriz o ContadorDeItens e o TotalDeItens
    int posX, posY; // Posição X e Y da sujeira
    for (int i = 0; i < numItems; i++) { // Puxa X itens e repete X vezes
        system("cls"); // Limpa a tela
        printf("Posicione as sujeiras:\n"); // Impressão convencional
        printMatrix(matrix, *itemCount, -1, -1, -1, -1); // Imprime a matriz atual

        // Solicita a posição da sujeira ao usuário e (N_itens + 1) imprime de forma mais didática ao usuário
        printf("\nSujeira %d\nLinha: ", i + 1);
        while (scanf("%d", &posX) != 1 || posX < 0 || posX >= ROWS) { // Excessão para ver se o valor informado está dentro ou fora da matriz
            printf("\nLinha inválida! Digite novamente: ");
            while (getchar() != '\n'); // Limpa o buffer de entrada
        }

        posY = 0; // Reseta Y

        do { // Bloco da posição do robo (Coluna)
            printf("Coluna: "); // Imprime a escolha de (coluna)
            while (scanf("%d", &posY) != 1 || posY < 0 || posY >= COLS) { // Verifica se o valor está dentro do limite de colunas (0 a 7)
                printf("\nColuna inválida! Digite novamente: ");
                while (getchar() != '\n'); // Limpa o buffer de entrada
            }
            break; // Para quebrar o laço do loop
        } while (1);

        // Verifica se a posição está ocupada
        while (matrix[posX][posY] != 0) { // Enquanto (True/Verdade) repita
            printf("\nPosição ocupada! Digite novamente:\nSujeira %d\nColuna: ", i + 1); // Excessão/Printa se ocupada
            while (scanf("%d", &posY) != 1 || posY < 0 || posY >= COLS) { // Verificando se posição inválida
                printf("\nColuna inválida! Digite novamente: "); // Printa se inválida
                while (getchar() != '\n'); // Limpa o buffer de entrada
            }
        }

        matrix[posX][posY] = 2; // Marca a posição da sujeira na matriz como 2
        system("cls"); // Limpa a tela
        printMatrix(matrix, *itemCount, -1, -1, -1, -1); // Imprime a matriz atual
    }
}

// Função para limpar a matriz
void clearMatrix(int matrix[ROWS][COLS]) {
    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            matrix[i][j] = 0; // Marca todas as células como vazias
        }
    }
}

// Função principal
int main() {
    setlocale(LC_ALL, "Portuguese"); // Permite acentuação usando o idioma selecionado
    int matrix[ROWS][COLS], numItems, itemCount; // Matrix (Linhas & Colunas), NúmeroTotalDeItens, ContadorDeItens de (0 à X)
    char playAgain; // Jogar novamente

    // Loop principal do programa / Bloco do game
    do { // Start do game
        itemCount = 0; // Reset de itens
        // Inicializa a matriz com zeros
        clearMatrix(matrix);

        int initialPosX, initialPosY; // Variável para salvar a posição inicial do robo (X & Y)
        int posX, posY; // Variável da posição do robo

        // Solicita a posição inicial do robô ao usuário
        do {
            printf("Escolha a posição inicial do robô\nDe 0 a 7\nLinha: "); // Printa a escolha de (linha)
            while (scanf("%d", &initialPosX) != 1 || initialPosX < 0 || initialPosX >= ROWS) { // Verifica se o valor está dentro da matiz (linhas) (0 a 7)
                printf("\nLinha inválida! Digite novamente: "); // Imprime erro
                while (getchar() != '\n'); // Limpa o buffer de entrada
            }

            do { // Bloco da posição do robo (Coluna)
                printf("Coluna: "); // Imprime a escolha de (coluna)
                while (scanf("%d", &initialPosY) != 1 || initialPosY < 0 || initialPosY >= COLS) { // Verifica se o valor está dentro da matriz (colunas) (0 a 7)
                    printf("\nColuna inválida! Digite novamente: ");
                    while (getchar() != '\n'); // Limpa o buffer de entrada
                }
                break;
            } while (1);

            break;
        } while (1);
        // Salva posição inicial
        posX = initialPosX; // De X
        posY = initialPosY; // De Y
        matrix[posX][posY] = 1; // Marca a posição inicial do robô na matriz como 1

        printf("\nEstado inicial do ambiente:\n");
        printMatrix(matrix, itemCount, posX, posY, initialPosX, initialPosY); // Imprime a matriz atual

        // Solicita ao usuário a quantidade de sujeiras a serem colocadas
        printf("\nQuantas sujeiras você deseja colocar? ");
        while (scanf("%d", &numItems) != 1 || numItems <= 0) {
            printf("\nNúmero inválido de sujeiras! Digite novamente: ");
            while (getchar() != '\n'); // Limpa o buffer de entrada
        }
        placeDirt(matrix, &itemCount, numItems); // Coloca as sujeiras na matriz

        // Solicita ao usuário os comandos para movimentar o robô
        printf("\nDigite 'W' para cima, 'A' para esquerda, 'S' para baixo, 'D' para direita\n");
        printf("Digite 'Q' para Desligar.\n");

        char move;
        // Loop para movimentar o robô enquanto houver sujeira próxima e ainda houver sujeiras não removidas
        while (itemCount < numItems && hasDirtNearby(matrix, posX, posY)) {
            scanf(" %c", &move); // Obtém o comando do usuário
            system("cls"); // Limpa a tela

            int nextX = posX, nextY = posY;

            // Calcula a próxima posição do robô de acordo com o comando recebido
            switch (tolower(move)) {
                case 'w': nextX = posX - 1; break;
                case 'a': nextY = posY - 1; break;
                case 's': nextX = posX + 1; break;
                case 'd': nextY = posY + 1; break;
                case 'q': printf("Desligando...\n"); return 0; // Encerra o programa se o usuário digitar 'Q'
                default: printf("\nComando inválido! Tente novamente.\n");
            }

            // Verifica se a próxima posição é válida e contém sujeira
            if (nextX >= 0 && nextX < ROWS && nextY >= 0 && nextY < COLS && matrix[nextX][nextY] == 2) {
                matrix[posX][posY] = 0; // Remove o robô da posição atual na matriz
                posX = nextX;
                posY = nextY;
                itemCount++; // Incrementa o contador de sujeiras removidas
                printf("Sujeira removida!\n");
                printf("\nNúmero de sujeiras removidas: %d\n", itemCount);
            } else {
                printf("Movimento inválido ou não há sujeira para remover!\n");
            }

            matrix[posX][posY] = 1; // Marca a nova posição do robô na matriz como 1
            printMatrix(matrix, itemCount, posX, posY, initialPosX, initialPosY); // Imprime a matriz atual

            // Verifica se todas as sujeiras foram removidas
            if (itemCount == numItems) printf("\nTodas as sujeiras foram removidas! Parabéns!\n");
        }

        // Verifica se todas as sujeiras foram removidas e o robô retornou à posição inicial
        if (itemCount == numItems && !hasDirtNearby(matrix, posX, posY)) {
            while (posX != initialPosX || posY != initialPosY) {
                // Prioriza verificação da posição Y quando o robô se moveu para esquerda ou direita
                if (posY > initialPosY && posX == initialPosX) {
                    matrix[posX][posY] = 0;
                    posY--;
                    printf("\nMovendo para a esquerda\n");
                } else if (posY < initialPosY && posX == initialPosX) {
                    matrix[posX][posY] = 0;
                    posY++;
                    printf("\nMovendo para a direita\n");
                }

                // Prioriza verificação da posição X quando o robô se moveu para cima ou para baixo
                if (posX > initialPosX) {
                    matrix[posX][posY] = 0;
                    posX--;
                    printf("\nMovendo para cima\n");
                } else if (posX < initialPosX) {
                    matrix[posX][posY] = 0;
                    posX++;
                    printf("\nMovendo para baixo\n");
                }
                matrix[posX][posY] = 1; // Marca a nova posição do robô na matriz como 1
                system("cls"); // Limpa a tela
                printMatrix(matrix, itemCount, posX, posY, initialPosX, initialPosY); // Imprime a matriz atual
                Sleep(900); // Espera um tempo para visualização
            }
            printf("\nRetornou à estação!\n");
        }

        // Verifica se não há sujeira próxima e ainda há sujeiras não removidas
        if (!hasDirtNearby(matrix, posX, posY) && itemCount < numItems) {
            printf("\nErro de locomoção! O sistema será encerrado.\n");
            printf("\nDeseja reiniciar o sistema? (S/N): ");
            scanf(" %c", &playAgain);
            system("cls");
            continue;
        }
        printf("\nDeseja reiniciar o sistema? (S/N): "); // Pergunta ao usuário se deseja reiniciar o sistema
        scanf(" %c", &playAgain);
        system("cls");

    } while (tolower(playAgain) == 's'); // Repete o loop enquanto o usuário desejar

    printf("\nDesligando...\n"); // Mensagem de encerramento
    return 0;
}
