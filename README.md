# Topic-modelling
A topic modelling on Augustine homilies, in particular those who have been reused by Caesarius, using R.
The studied sermons correspond to the rows of the .csv file. They all belong to _Quinquaginta_ collection. The different traditions of one sermon has been digitally edited according to the apparatus in the modern editions. The versions whose name starts by an "A" are from Augustine (for example, A_AU_s_142_DVD) and those by a "C" are from Caesarius (for example, C_AU_s_142Q). In this example, "DVD" means the _De Verbis Domini_ collection and Q the _Quinquaginta_ collection.

```ruby
library("stm")
library("readr")
data=read_csv("OUTPUT-FILE.csv")
processed <- textProcessor(data$texts, metadata = data)

out <- prepDocuments(processed$documents, processed$vocab,
                     processed$meta)

#établissement d'une sparse document-term matrix "docs" avec une ligne par document et une colonne par terme
#(Chaque document est représenté par une matrice de nombres entiers de deux lignes et 
#des valeurs de colonnes égales au nombre de mots de vocabulaire uniques présents dans le document)
#(La première ligne contient l'entrée de vocabulaire indexée 1 et la deuxième ligne 
#contient le nombre de fois que ce terme apparaît)
docs <- out$documents
#Vecteur de caractères spécifiant les mots du corpus dans l'ordre alphabétique 
#Chaque terme de l'index de vocabulaire doit apparaître au moins une fois dans les documents.
vocab <- out$vocab
meta <- out$meta

#Première étape de l'EM : estimation du Structural Topic Model d'après une méthode 
#d'espérance-maximisation (EM) variationnelle non conjuguée.
#K est est le nombre désiré de topics (20)
#méthode utilisée : Latent Dirichlet Alocation (LDA)
#nombre d'itérations EM fixé à 75
PrevFit <- stm(documents = out$documents, vocab = out$vocab,
                         K = 20, prevalence =~ Auteur, max.em.its = 75,
                         data = meta, init.type = "LDA")

#Deuxième étape de l'EM
Content <- stm(out$documents, out$vocab, K = 20,
                       prevalence =~ Auteur, content =~ Auteur,
                       max.em.its = 75, data = meta, init.type = "LDA")

#pour les topics 1,7,20, identification de leurs mots les plus représentatifs, de leurs mots covariables 
#en fonction des auteurs (Augustin ou Césaire), des covariables d'interaction entre les topics
labelTopics(Content, c(1, 7, 20), frexweight = 0.5)
plot.STM(Content, type = "labels", topics = c(1,7,20))

#graphique affichant les mots des topics et leur fréquence dans le corpus
plot.STM(Content, type = "summary", xlim = c(0, 0.3))
#affichage de mots du topic 2 qui sont plus associés à un auteur qu'à un autre
labelTopics(Content, c(2), frexweight = 0.5)
plot.STM(Content, type = "perspectives", topics = 2)
#calcul de la différence de probabilité d'un mot pour les topics 16 et 18, 
#normalisée par la différence maximale de probabilité chez Augustin ou chez Césaire 
#de n'importe quel mot entre les deux topics
labelTopics(Content, c(16,18), frexweight = 0.5)
plot.STM(Content, type = "perspectives", topics = c(16, 18))
#nuage de mots du topic 13 relativement à la fréquence d'apparition de ses mots dans le corpus
cloud(Content, topic = 13, scale = c(2, 0.25))
```
