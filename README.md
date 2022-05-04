# Topic-modelling
A topic modelling on Augustine homilies, in particular those who have been reused by Caesarius, using R.
The studied sermons correspond to the rows of the .csv file. They all belong to _Quinquaginta_ collection. The different traditions of one sermon has been digitally edited according to the apparatus in the modern editions. The versions whose name starts by an "A" are from Augustine (for example, A_AU_s_142_DVD) and those by a "C" are from Caesarius (for example, C_AU_s_142Q). In this example, "DVD" means the _De Verbis Domini_ collection and Q the _Quinquaginta_ collection.

```ruby
library("stm")
library("readr")
library("wordcloud")
data=read.csv("OUTPUT-FILE_no_doublon.csv", sep=",")
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

#Estimation du Structural Topic Model d'après une méthode 
#d'espérance-maximisation (EM) variationnelle non conjuguée.
#K est est le nombre désiré de topics (20)
#méthode utilisée : Latent Dirichlet Alocation (LDA)
#nombre d'itérations EM fixé à 75

#Prevalence Topic
PrevFit <- stm(documents = out$documents, vocab = out$vocab,
               K = 20, prevalence =~ Auteur, max.em.its = 75,
               data = meta, init.type = "LDA")

#Topic Content
Content <- stm(out$documents, out$vocab, K = 20,
               prevalence =~ Auteur, content =~ Auteur,
               max.em.its = 75, data = meta, init.type = "LDA")

#Liste des topics en fonction des cinq mots qui les caractérisent le mieux
labelTopics(Content, n=5, frexweight = 0.5)

#graphique affichant les mots des topics et leur fréquence dans le corpus
plot.STM(Content, type = "summary", n=5, xlim = c(0, 0.3))

```
