#include <stdio.h>
#include <stdlib.h>

// Définition des couleurs des joueurs
typedef enum {
    Rouge,
    Vert,
    Bleu,
    Jaune    
} Couleur_joueur;

// Définition des constantes pour les types de pions et les couleurs
#define PION_BLEU 1
#define DAME_BLEU 2
#define PION_ROUGE 3
#define DAME_ROUGE 4
#define PION_JAUNE 5
#define DAME_JAUNE 6
#define PION_VERT 7
#define DAME_VERT 8
#define CASE_VIDE 0
#define CASE_NOIRE 9

#define TAILLE 17 // Taille du plateau (17x17)

// Codes ANSI pour les couleurs
#define RESET "\033[0m"   // Réinitialisation des styles
#define FOND_CYAN "\033[46m" // Fond cyan
#define FOND_NOIR "\033[40m"  // Fond noir
#define FOND_BLANC "\033[47m" // Fond blanc
#define TXT_BLEU "\033[34m"   // Texte bleu

// Structure du plateau de jeu
typedef struct {
    int index[TAILLE][TAILLE]; // État du plateau
    int nb_bleu, nb_rouge, nb_jaune, nb_vert; // Nombre de pions pour chaque couleur
} Damier;

// Vérifie si une cellule appartient à l'un des 4 coins cyans de la matrice qui sont non affectables.
int Coin_Cyan(int i, int j) {
    return ((i >= 0 && i < 4 && j >= 0 && j < 4) ||       // Coin supérieur gauche
            (i >= 13 && i < TAILLE && j >= 0 && j < 4) || // Coin inférieur gauche
            (i >= 0 && i < 4 && j >= 13 && j < TAILLE) || // Coin supérieur droit
            (i >= 13 && i < TAILLE && j >= 13 && j < TAILLE)); // Coin inférieur droit
}

// Initialise le damier avec les pions dans les positions de départ.
void init_damier(Damier* jeu) {
    jeu->nb_bleu = 22;
    jeu->nb_rouge = 22;
    jeu->nb_jaune = 22;
    jeu->nb_vert = 22;

    // Initialisation du tableau
    for (int i = 0; i < TAILLE; i++) {
        for (int j = 0; j < TAILLE; j++) {
            jeu->index[i][j] = CASE_VIDE;
        }
    }
    // Placement des pions sur les cases
    for (int i = 0; i < 5; i++) {
        for (int j = 4; j < 13; j++) {
            if ((i + j) % 2 == 1) {
                jeu->index[i][j] = PION_BLEU;
            }
        }
    }
    for (int i = 12; i < 17; i++) {
        for (int j = 4; j < 13; j++) {
            if ((i + j) % 2 == 1) {
                jeu->index[i][j] = PION_ROUGE;
            }
        }
    }
    for (int i = 4; i < 13; i++) {
        for (int j = 12; j < TAILLE; j++) {
            if ((i + j) % 2 == 1) {
                jeu->index[i][j] = PION_JAUNE;
            }
        }
    }
    for (int i = 4; i < 13; i++) {
        for (int j = 0; j < 5; j++) {
            if ((i + j) % 2 == 1) {
                jeu->index[i][j] = PION_VERT;
            }
        }
    }
}

// Affiche une cellule du plateau
void afficher_Cellule(int i, int j, int index[TAILLE][TAILLE]) {
    if (Coin_Cyan(i, j)) {
        printf(FOND_CYAN "   " RESET); // Fond cyan sans traits noirs
    } else {
        switch (index[i][j]) {
            case PION_BLEU:
                printf(FOND_NOIR TXT_BLEU " ● " RESET);
                break;
            case DAME_BLEU:
                printf(FOND_NOIR TXT_BLEU "[O]" RESET);
                break;
            case PION_ROUGE:
                printf(FOND_NOIR "\033[31m ● " RESET); // Rouge
                break;
            case DAME_ROUGE:
                printf(FOND_NOIR "\033[31m[O]" RESET);
                break;
            case PION_JAUNE:
                printf(FOND_NOIR "\033[33m ● " RESET); // Jaune
                break;
            case DAME_JAUNE:
                printf(FOND_NOIR "\033[33m[O]" RESET);
                break;
            case PION_VERT:
                printf(FOND_NOIR "\033[32m ● " RESET); // Vert
                break;
            case DAME_VERT:
                printf(FOND_NOIR "\033[32m[O]" RESET);
                break;
            case CASE_VIDE:
                // Alternance blanc/noir pour les cases vides
                if ((i + j) % 2 == 0) {
                    printf(FOND_BLANC "   " RESET);
                } else {
                    printf(FOND_NOIR "   " RESET);
                }
                break;
            default:
                printf(" ? "); // Cas invalide
                break;
        }
    }
}

// Affiche le damier complet avec les indices en haut et à gauche.
void afficher_Damier(Damier* jeu) {
    printf("    "); 
    for (int j = 0; j < TAILLE; j++) {
        printf(TXT_BLEU "%c  " RESET, 'a'+ j);
    }
    printf("\n");
    for (int i = 0; i < TAILLE; i++) {
        printf(TXT_BLEU " %c " RESET, 'A' + i);
        for (int j = 0; j < TAILLE; j++) {
            afficher_Cellule(i, j, jeu->index);
        }
        printf("\n");
    }
}

// Effectue une rotation de 90° dans le sens des aiguilles d'une montre
Damier* rotationDamier(Damier* jeu, Damier* temp) {
    for (int i = 0; i < TAILLE; i++) {
        for (int j = 0; j < TAILLE; j++) {
            temp->index[j][TAILLE - i - 1] = jeu->index[i][j];
        }
    }
    return temp;
}

// Affiche le damier avec la rotation appliquée
void afficherDamier(Damier* jeu, Damier* temp, Couleur_joueur couleur) {
    switch(couleur) {
        case Rouge:
            printf("    "); 
            for (int j = 0; j < TAILLE; j++) {
                printf(TXT_BLEU "%c  " RESET, 'a' + j);
            }
            printf("\n");
            for (int i = 0; i < TAILLE; i++) {
                printf(TXT_BLEU " %c " RESET, 'A' + i);
                for (int j = 0; j < TAILLE; j++) {
                    afficher_Cellule(i, j, temp->index);
                }
                printf("\n");
            }
            break;
        case Jaune:
            // Affichage modifié pour la couleur Jaune
            printf("    "); 
            for (int j = 0; j < TAILLE; j++) {
                printf(TXT_BLEU "%c  " RESET, 'A' + j);
            }
            printf("\n");
            for (int i = 0; i < TAILLE; i++) {
                printf(TXT_BLEU " %c " RESET, 'q' - i);
                for (int j = 0; j < TAILLE; j++) {
                    afficher_Cellule(i, j, temp->index);
                }
                printf("\n");
            }
            break;
        case Bleu:
            // Affichage modifié pour la couleur Bleu
            printf("    "); 
            for (int j = 0; j < TAILLE; j++) {
                printf(TXT_BLEU "%c  " RESET, 'q' - j);
            }
            printf("\n");
            for (int i = 0; i < TAILLE; i++) {
                printf(TXT_BLEU " %c " RESET, 'Q' - i);
                for (int j = 0; j < TAILLE; j++) {
                    afficher_Cellule(i, j, temp->index);
                }
                printf("\n");
            }
            break;
        case Vert:
            // Affichage modifié pour la couleur Vert
            printf("    "); 
            for (int j = 0; j < TAILLE; j++) {
                printf(TXT_BLEU "%c  " RESET, 'Q' - j);
            }
            printf("\n");
            for (int i = 0; i < TAILLE; i++) {
                printf(TXT_BLEU " %c " RESET, 'a' + i);
                for (int j = 0; j < TAILLE; j++) {
                    afficher_Cellule(i, j, temp->index);
                }
                printf("\n");
            }
            break;
    }
}

// Rotation de la couleur du joueur
void Rotation_Couleur(Damier* jeu, Damier* temp1, Damier* temp2, Damier* temp3, Couleur_joueur couleur) {
    switch (couleur) {
        case Rouge:
            afficher_Damier(jeu);
            break;
        case Jaune:
            afficherDamier(rotationDamier(jeu, temp1), temp1, Jaune);
            break;
        case Bleu:
            afficherDamier(rotationDamier(rotationDamier(jeu, temp1), temp2), temp2, Bleu);
            break;
        case Vert:
            afficherDamier(rotationDamier(rotationDamier(rotationDamier(jeu, temp1), temp2), temp3), temp3, Vert);
            break;
    }
}

// Point d'entrée principal
int main() {
    Damier jeu, temp, temp1, temp2, temp3;
    init_damier(&jeu);

    // Affichage initial
    printf("Damier avec pions et couleurs :\n\n");
    afficher_Damier(&jeu);

    // Affichage pour chaque couleur de joueur
    printf("\nDamier pour le joueur Vert :\n");
    Rotation_Couleur(&jeu, &temp1, &temp2, &temp3, Vert);

    printf("\nDamier pour le joueur Rouge :\n");
    Rotation_Couleur(&jeu, &temp1, &temp2, &temp3, Rouge);

    printf("\nDamier pour le joueur Jaune :\n");
    Rotation_Couleur(&jeu, &temp1, &temp2, &temp3, Jaune);

    printf("\nDamier pour le joueur Bleu :\n");
    Rotation_Couleur(&jeu, &temp1, &temp2, &temp3, Bleu);

    return 0;
}
