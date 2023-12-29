#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char* nom;
    char* vote;
} Candidat;

typedef struct Election {
    int annee;
    Candidat val;
    char resultat[50];
    int nbV;
    struct Election* suiv;
} Election;

typedef struct Cellule {
    Election* val;
    int annee;
    struct Cellule* suiv;
} Cellule;

Election* creerElection(int annee) {
    Election* nouvelleElection = (Election*)malloc(sizeof(Election));
    nouvelleElection->annee = annee;
    nouvelleElection->suiv = NULL;
    nouvelleElection->nbV = 0;
    return nouvelleElection;
}

void ajoutCandidat(char* nom, char* vote, int annee, Cellule** listeElections) {
    Cellule* celluleCourante = *listeElections;

    while (celluleCourante != NULL) {
        if (celluleCourante->annee == annee) {
            Election* electionCourante = celluleCourante->val;
            Candidat candidat;
            candidat.nom = strdup(nom);
            candidat.vote = strdup(vote);

            Election* nouvelleElection = (Election*)malloc(sizeof(Election));
            nouvelleElection->val = candidat;
            nouvelleElection->suiv = NULL;
            nouvelleElection->nbV = 0;

            while (electionCourante->suiv != NULL) {
                electionCourante = electionCourante->suiv;
            }

            electionCourante->suiv = nouvelleElection;
            return;
        }

        celluleCourante = celluleCourante->suiv;
    }

    printf("Election introuvable\n");
}

void ajoutElection(Election* election, Cellule** listeElections) {
    Cellule* celluleCourante = *listeElections;

    while (celluleCourante != NULL && celluleCourante->suiv != NULL) {
        celluleCourante = celluleCourante->suiv;
    }

    Cellule* nouvelleCellule = (Cellule*)malloc(sizeof(Cellule));
    nouvelleCellule->val = election;
    nouvelleCellule->suiv = NULL;

    if (celluleCourante == NULL) {
        *listeElections = nouvelleCellule;
    } else {
        celluleCourante->suiv = nouvelleCellule;
    }
}

void voteElection(Election* election) {
    Election* courante = election;
    Election* comparaison = election;

    while (courante != NULL) {
        while (comparaison != NULL) {
            if (strcmp(courante->val.nom, comparaison->val.vote) == 0) {
                courante->nbV++;
            }
            comparaison = comparaison->suiv;
        }
        courante = courante->suiv;
        comparaison = election;
    }
}

void changerVote(Cellule* listeElections, int annee, char nom[50], char V[50]) {
    Cellule* celluleCourante = listeElections;

    while (celluleCourante != NULL) {
        if (celluleCourante->annee == annee) {
            Election* electionCourante = celluleCourante->val;

            while (electionCourante != NULL) {
                if (strcmp(electionCourante->val.nom, nom) == 0) {
                    strcpy(electionCourante->val.vote, V);
                    return;
                }
                electionCourante = electionCourante->suiv;
            }
        }
        celluleCourante = celluleCourante->suiv;
    }

    printf("Candidat introuvable\n");
}

void afficherElection(Cellule* listeElections, int annee) {
    Cellule* celluleCourante = listeElections;

    while (celluleCourante != NULL) {
        if (celluleCourante->annee == annee) {
            Election* electionCourante = celluleCourante->val;

            while (electionCourante != NULL) {
                printf("%s %d %s\n", electionCourante->val.nom, electionCourante->nbV, electionCourante->resultat);
                electionCourante = electionCourante->suiv;
            }
            return;
        }
        celluleCourante = celluleCourante->suiv;
    }
}

void afficherListeVote(Cellule* listeElections, int annee, char nom[50]) {
    Cellule* celluleCourante = listeElections;

    while (celluleCourante != NULL) {
        if (celluleCourante->annee == annee) {
            Election* electionCourante = celluleCourante->val;

            while (electionCourante != NULL) {
                if (strcmp(electionCourante->val.vote, nom) == 0) {
                    printf("%s\n", electionCourante->val.nom);
                }
                electionCourante = electionCourante->suiv;
            }
            return;
        }
        celluleCourante = celluleCourante->suiv;
    }
}

void miseAJour(Cellule* listeElections) {
    Cellule* celluleCourante = listeElections;
    Election* maxElection = NULL;

    while (celluleCourante != NULL) {
        Election* courante = celluleCourante->val;

        while (courante != NULL) {
            if (maxElection == NULL || courante->nbV > maxElection->nbV) {
                maxElection = courante;
            }
            courante = courante->suiv;
        }

        celluleCourante = celluleCourante->suiv;
    }

    celluleCourante = listeElections;

    while (celluleCourante != NULL) {
        Election* courante = celluleCourante->val;

        while (courante != NULL) {
            if (courante == maxElection) {
                strcpy(courante->resultat, "a ete elu");
            } else {
                strcpy(courante->resultat, "n'est pas elu");
            }
            courante = courante->suiv;
        }

        celluleCourante = celluleCourante->suiv;
    }
}

void libererMemoire(Cellule* listeElections) {
    while (listeElections != NULL) {
        Cellule* temp = listeElections;
        listeElections = listeElections->suiv;

        Election* electionCourante = temp->val;
        Election* suivElection;

        while (electionCourante != NULL) {
            suivElection = electionCourante->suiv;
            free(electionCourante->val.nom);
            free(electionCourante->val.vote);
            free(electionCourante);
            electionCourante = suivElection;
        }

        free(temp);
    }
}

int main() {
    Cellule* listeElections = NULL;
    int r = 1;

    while (r != 6) {
        printf("\n1. Ajout candidat\n");
        printf("2. Creer election\n");
        printf("3. Change Vote\n");
        printf("4. Affiche election\n");
        printf("5. Affiche liste de vote\n");
        printf("6. Quitter\n");
        printf("Choix: ");
        scanf("%d", &r);

        if (r == 6) {
            printf("Au revoir!\n");
        } else if (r == 5) {
            char nomm[50];
            int aaan;
            printf("ANNEE: ");
            scanf("%d", &aaan);
            printf("NOM: ");
            scanf("%s", nomm);
            afficherListeVote(listeElections, aaan, nomm);
        } else if (r == 4) {
            int aanne;
            printf("ANNEE: ");
            scanf("%d", &aanne);
            afficherElection(listeElections, aanne);
        } else if (r == 3) {
            int annne;
            char nom[50], vote[50];
            printf("ANNEE: ");
            scanf("%d", &annne);
            printf("NOM: ");
            scanf("%s", nom);
            printf("VOTE: ");
            scanf("%s", vote);
            changerVote(listeElections, annne, nom, vote);
            Cellule* celluleCourante = listeElections;

            while (celluleCourante != NULL) {
                Election* electionCourante = celluleCourante->val;
                if (celluleCourante->annee == annne) {
                    voteElection(electionCourante);
                    printf("%s a ete elu\n", electionCourante->val.nom);
                }
                celluleCourante = celluleCourante->suiv;
            }
        } else if (r == 1) {
            char* noomm = (char*)malloc(50 * sizeof(char));
            char* votee = (char*)malloc(50 * sizeof(char));
            int anne;
            printf("NOM: ");
            scanf("%s", noomm);
            printf("VOTE: ");
            scanf("%s", votee);
            printf("ANNEE: ");
            scanf("%d", &anne);
            ajoutCandidat(noomm, votee, anne, &listeElections);
            Cellule* celluleCourante = listeElections;

            while (celluleCourante != NULL) {
                Election* electionCourante = celluleCourante->val;
                if (celluleCourante->annee == anne) {
                    voteElection(electionCourante);
                    printf("%s a ete elu\n", electionCourante->val.nom);
                }
                celluleCourante = celluleCourante->suiv;
            }
        } else if (r == 2) {
            int annee;
            printf("ANNEE: ");
            scanf("%d", &annee);
            Election* nouvelleElection = creerElection(annee);
            ajoutElection(nouvelleElection, &listeElections);
            voteElection(nouvelleElection);
            printf("%s a ete elu\n", nouvelleElection->val.nom);
        }

        miseAJour(listeElections);
    }

    libererMemoire(listeElections);

    return 0;
}
