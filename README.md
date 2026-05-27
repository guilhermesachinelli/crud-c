#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {

    char nome[100];
    char nascimento[20];
    char telefone[20];

} Pessoa;

/* ========================= CADASTRAR ========================= */

void cadastrar() {

    FILE *arquivo;

    Pessoa p;

    int valido;
    int tamanho;
    int i;

    arquivo = fopen("pessoas.txt", "a");

    if (arquivo == NULL) {

        printf("\nErro ao abrir arquivo!\n");
        return;
    }

    printf("\n========== CADASTRAR PESSOA ==========\n");

    printf("Nome: ");
    fgets(p.nome, 100, stdin);
    p.nome[strcspn(p.nome, "\n")] = 0;

    printf("Data de nascimento: ");
    fgets(p.nascimento, 20, stdin);
    p.nascimento[strcspn(p.nascimento, "\n")] = 0;

    valido = 0;

    while (valido == 0) {

        printf("Telefone (11 numeros): ");

        fgets(p.telefone, 20, stdin);

        p.telefone[strcspn(p.telefone, "\n")] = 0;

        tamanho = strlen(p.telefone);

        if (tamanho != 11) {

            printf("\nTelefone invalido! Digite exatamente 11 numeros.\n\n");

        } else {

            valido = 1;

            for (i = 0; i < tamanho; i++) {

                if (p.telefone[i] < '0' || p.telefone[i] > '9') {

                    valido = 0;
                }
            }

            if (valido == 0) {

                printf("\nTelefone invalido! Use apenas numeros.\n\n");
            }
        }
    }

    fprintf(arquivo, "%s;%s;%s\n",
            p.nome,
            p.nascimento,
            p.telefone);

    fclose(arquivo);

    printf("\nPessoa cadastrada com sucesso!\n");
}

/* ========================= LISTAR ========================= */

void listar() {

    FILE *arquivo;

    char linha[200];

    arquivo = fopen("pessoas.txt", "r");

    if (arquivo == NULL) {

        printf("\nNenhuma pessoa cadastrada.\n");
        return;
    }

    printf("\n========== LISTA DE PESSOAS ==========\n");

    while (fgets(linha, 200, arquivo) != NULL) {

        char copia[200];

        char *nome;
        char *nascimento;
        char *telefone;

        strcpy(copia, linha);

        nome = strtok(copia, ";");
        nascimento = strtok(NULL, ";");
        telefone = strtok(NULL, "\n");

        printf("\nNome: %s\n", nome);
        printf("Nascimento: %s\n", nascimento);
        printf("Telefone: %s\n", telefone);
    }

    fclose(arquivo);
}

/* ========================= BUSCAR ========================= */

void buscar() {

    FILE *arquivo;

    char linha[200];
    char telefoneBusca[20];

    int encontrou = 0;

    arquivo = fopen("pessoas.txt", "r");

    if (arquivo == NULL) {

        printf("\nNenhuma pessoa cadastrada.\n");
        return;
    }

    printf("\nDigite o telefone da pessoa: ");

    fgets(telefoneBusca, 20, stdin);

    telefoneBusca[strcspn(telefoneBusca, "\n")] = 0;

    while (fgets(linha, 200, arquivo) != NULL) {

        char copia[200];

        char *nome;
        char *nascimento;
        char *telefone;

        strcpy(copia, linha);

        nome = strtok(copia, ";");
        nascimento = strtok(NULL, ";");
        telefone = strtok(NULL, "\n");

        if (strcmp(telefoneBusca, telefone) == 0) {

            printf("\n========== PESSOA ENCONTRADA ==========\n");

            printf("Nome: %s\n", nome);
            printf("Nascimento: %s\n", nascimento);
            printf("Telefone: %s\n", telefone);

            encontrou = 1;
        }
    }

    if (encontrou == 0) {

        printf("\nPessoa nao encontrada.\n");
    }

    fclose(arquivo);
}

/* ========================= REMOVER ========================= */

void removerPessoa() {

    FILE *arquivo;
    FILE *temp;

    char linha[200];
    char telefoneBusca[20];

    int encontrou = 0;

    arquivo = fopen("pessoas.txt", "r");

    if (arquivo == NULL) {

        printf("\nNenhuma pessoa cadastrada.\n");
        return;
    }

    temp = fopen("temp.txt", "w");

    printf("\nDigite o telefone da pessoa que deseja remover: ");

    fgets(telefoneBusca, 20, stdin);

    telefoneBusca[strcspn(telefoneBusca, "\n")] = 0;

    while (fgets(linha, 200, arquivo) != NULL) {

        char copia[200];

        char *telefone;

        strcpy(copia, linha);

        strtok(copia, ";");
        strtok(NULL, ";");
        telefone = strtok(NULL, "\n");

        if (strcmp(telefoneBusca, telefone) != 0) {

            fprintf(temp, "%s", linha);

        } else {

            encontrou = 1;
        }
    }

    fclose(arquivo);
    fclose(temp);

    remove("pessoas.txt");
    rename("temp.txt", "pessoas.txt");

    if (encontrou == 1) {

        printf("\nPessoa removida com sucesso!\n");

    } else {

        printf("\nTelefone nao encontrado.\n");
    }
}

/* ========================= MAIN ========================= */

int main() {

    int op;

    do {

        printf("\n=====================================\n");
        printf("         CRUD DE PESSOAS            \n");
        printf("=====================================\n");

        printf("[1] Cadastrar Pessoa\n");
        printf("[2] Listar Pessoas\n");
        printf("[3] Buscar Pessoa\n");
        printf("[4] Excluir Pessoa\n");
        printf("[0] Sair\n");

        printf("\nOpcao: ");

        scanf("%d", &op);

        getchar();

        switch (op) {

            case 1:
                cadastrar();
                break;

            case 2:
                listar();
                break;

            case 3:
                buscar();
                break;

            case 4:
                removerPessoa();
                break;

            case 0:
                printf("\nEncerrando programa...\n");
                break;

            default:
                printf("\nOpcao invalida!\n");
        }

    } while (op != 0);

    return 0;
}
