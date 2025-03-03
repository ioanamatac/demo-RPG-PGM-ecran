# ✅demo-RPG-PGM-ecran
# 🐬Utilisation des ecrans RPG AS400


## Table of Contents 🐛
1. [General Info](#general-info)
2. [Technologies](#technologies)
3. [Définition fichier](#définition-fichier) 
4. [Définition écran](#définition-écran) 
5. [Définition variable](#definition-variable) 
6. [Sous routine: Générer le code employé](#sous-routine-générer-le-code-employé)
7. [Sous routine: Validation des champs](#sous-routine-validation-des-champs)
8. [Sous routine: Enregistrement des valeurs](#sous-routine-enregistrement-des-valeurs)
   
# 🦕General Info


>Création d'un programme RPG 🚀 EMPLOY01

>Liaison écran femploy01fm à ce PGM 👌


# 🀄Technologies

* Système d'exploitation: AS 400 
* Language : RPG
  
# 🎬Definition fichier

 >* Nom source employe

> * Type file i (imput file)

>* File designation(full procedural) - Le contrôle se fera au sein du programme

> * File addition A -  Add records
 
>* Format file E - Externally

>* Record Address Type K - Pour les valeurs clés à utiliser pour les fichiers décrits en externe

> * Device DISK 


# 🎬Definition écran
> * Les écrans sont définis dans la feuille F 

 >* Nom ecran femploy01fm

> * Type c (input/output)

>* File designation(full procedural) - Le contrôle se fera au sein du programme

> * File format E - Il s'agit d'un fichier de script externe 

> * Device Workstn - Les ecran qu’on crees ds AS400

# ⚡Definition variable

>* Je vais me définir une variable, une variable temporaire iValTemp pour enregistrer ce que je lis. Les écrans ont des variables, c'est là que je vais déposer cette valeur.  

# 🏯Sous-routine: Générer le code employé

>* Je veux que le code sur l'écran soit généré automatiquement par le programme RPG, puisque je ne veux pas donner à l'utilisateur la liberté de saisir le code.

>* Je dois lire dans le fichier quel est le dernier code, mais le dernier code généré.
>* Ce sous-programme va positionner le curseur à la fin du dernier enregistrement du fichier.
Le fichier est classé par code d'employé qui doit être du plus bas au plus élevé. Je fais une répétition pour lire une valeur précédente.

>* Tous les champs à l'écran sont en caractères, mais dans le fichier le code est numérique. Ensuite, la function EditC me permet de transformer les chiffres en char et de remplir la gauche avec zéro pour afficher ce code à l'écran.

# ⛩️Sous routine: Validation des champs

> * L'écran reste dans la boucle, il continue à être affiché jusqu'à ce que l'utilisateur ou pendant que l'utilisateur n'appuyez pas sur la touche F12.

>  * J'ai ajouté une validation pour le champ prénom, une validation pour le champ nom, une validation pour la date de naissance et une validation pour le poste.
Il faut qu'au moins quatre champs soient remplis sinon une alerte s'affiche à l'écran pour dire à l'utilisateur qu'il doit remplir ces champs.

> * Si le champ est vide l'alerte me dira Champ d'identification vide. J'appuie sur Entrée et le message est supprimé.

>* A l'execution de la sous routine si tout va bien, j'enregistre, je montre l'écran, je lui dis qu'il est là et je le lui montre à nouveau lécran pour qu'il puisse insérer une autre valeur.

# 🛕Sous routine: Enregistrement des valeurs

> * Une fois toutes les validations terminées, j’enregistre toutes les valeurs qui me viennent de l'écran, qu'il y a dans le fichier.

>* La valeur de l'affichage est char. Je dois donc transformer cette valeur en valeur numérique car ce champ est numérique.

>* Pour cela, j'ai besoin du premier champ qui est la chaîne, e deuxième champ sera la longueur que le champ aura et le troisième le nombre de caractères numériques. des décimales.

>* Juste au cas où, supprimer tous les espaces à gauche et à droite et enregistrez ceci aussi clairement que possible dans le champ.

> * J'enregistre et j'affiche un message à l'utilisateur indiquant que son enregistrement est là, il a été enregistré avec succès.👌


```RPG
 Columns . . . :    6  76            Edit                      IOANA1/QRPGLESRC 
 SEU==>                                                                EMPLOY01 
 FMT *   *. 1 ...+... 2 ...+... 3 ...+... 4 ...+... 5 ...+... 6 ...+... 7 ...+. 
        *************** Beginning of data ************************************* 
0001.00  * *******************************************************              
0004.00  * L'objectif: Maintenance des employes                  *              
0005.00  *********************************************************              
0005.05  * L'objectif: Ecran lie a ce programme:employ01fm       *              
0005.06  *********************************************************              
0005.08  *Definition: enregistrer ds un fichier                                 
0006.00 femploye   if a e           k DISK                                      
0006.01 femploy01fmcf   e             WORKSTN                                   
0007.03  *                                                                      
0007.04  *Definition de variables                                               
0007.05 diValTmp          s              9s 0                                   
0037.04  *********************************************************              
0037.05  * Debut de programme                                    *              
0037.06  *********************************************************              
0037.07 c                   exsr      srgencod                                  
0037.08 c                   dow       not(*in12)                                
0037.09 c                   exfmt     reg01                                  
0037.11 c                   if        *in01                                  
0037.12 c                   exsr      srval                                  
0037.13 c                   endif                                            
0037.14 c                   if        sr01msg = *blanks                      
0037.15 c                   exsr      srenr                                  
0037.17 c                   endif                                            
0037.18 c                   enddo                                            
0037.19 c                   eval      *inlr = *on                            
0037.20  *********************************************************        
0037.21  * Sous-routine pour generer le code employe         *            
0037.22  *********************************************************        
0037.23 c     srgencod      Begsr                                         
0037.24 c     *hival        setll     employe                             
0037.25 c                   readp     employe                             
0037.26 c                   if        not %eof                            
0037.27 c                   eval      ivaltmp = empcod + 1                
0037.28 c                   eval      sr01cod = %EditC(ivaltmp:'X')       
0037.29 c*                  move      ivaltmp       sr01cod              
0037.30 c                   else                                         
0037.31 c                   eval      sr01cod = '000000001'              
0037.32 c                   endif                                        
0037.33 c                   endsr                                        
0037.34  *********************************************************       
0037.35  *Sous-routine pour valider les champs d'entree         *        
0037.36  *********************************************************       
0037.37 c     srval         Begsr                                        
0038.00 c                   if        sr01idn = *blanks             
0038.01 c                   eval      sr01msg ='Champ identifiant'+ 
0038.02 c                             'en blanc'                    
0038.03 c                   exfmt     reg01                         
0038.04 c                   endif                                   
0038.06 c                   if        sr01fna = *blanks             
0038.07 c                   eval      sr01msg ='Champ nom'+         
0038.08 c                             'en blanc'                    
0038.09 c                   exfmt     reg01                         
0039.01 c                   if        sr01lna = *blanks                  
0039.02 c                   eval      sr01msg ='Champ prenom' +          
0039.03 c                             'en blanc'                         
0039.05 c                   exfmt     reg01                              
0039.06 c                   endif                                        
0040.01 c                   if        sr01bod = *blanks                  
0040.02 c                   eval      sr01msg ='Date naissance'+         
0040.03 c                             'en blanc'                         
0040.05 c                   exfmt     reg01                              
0040.06 c                   endif                                        
0042.00 c                   if        sr01job = *blanks                  
0043.00 c                   eval      sr01msg ='Metier' +                 
0043.01 c                             'en blanc'                         
0045.00 c                   exfmt     reg01                              
0046.00 c                   endif                                        
0046.01 c                   endsr                                        
0046.03  *********************************************************       
0046.04  * Subroutine pour enregistrer dans le fichier employe   *  
0046.05  *********************************************************  
0046.06 c     srenr         Begsr                                   
0046.07 c                   eval      empcod = %dec(sr01cod:9:0)    
0046.08 c                   eval      empidn = %Trim(sr01idn)       
0046.09 c                   eval      empfna = %Trim(sr01fna)       
0046.10 c                   eval      emplna = %Trim(sr01lna)       
0046.11 c                   eval      empbod = %Dec(sr01bod:8:0)    
0046.12 c*                  eval      empjob = %Trim(sr01job)       
0046.13 c                   write     remploye                                 
0046.14 c                   eval      sr01msg = 'Enregistrement sauvegarde'    
0046.15 c                             + 'avec succes'                          
0046.16 c                   exfmt     reg01                                    
0047.00 c                   endsr                                              
        ****************** End of data ****************************************

```
> Cargo = Metier pas marchandise(on s'en fout)🥳
> 
![Ecran + PGM EMPLOY01](https://github.com/user-attachments/assets/a1316516-372f-4480-91c5-a3c522cc018e)

![Ecriture fichier EMPLOY](https://github.com/user-attachments/assets/e2627e47-e7cb-4992-9030-7cb12387ae14)



>Enjoy !🫶
>Ioana
