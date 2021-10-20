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

docs <- out$documents
vocab <- out$vocab
meta <- out$meta

levels(meta$rating)

PrevFit <- stm(documents = out$documents, vocab = out$vocab,
                         K = 20, prevalence =~ Auteur, max.em.its = 75,
                         data = out$meta, init.type = "Spectral")


Select <- selectModel(out$documents, out$vocab, K = 20,
                                prevalence =~ Auteur, max.em.its = 75, data = out$meta,
                                runs = 20, seed = 8458159)


Content <- stm(out$documents, out$vocab, K = 20,
                       prevalence =~ Auteur, content =~ Auteur,
                       max.em.its = 75, data = out$meta, init.type = "Spectral")

plot(Content, type = "summary", xlim = c(0, 0.3))
plot(Content, type = "perspectives", topics = 10)
plot(Content, type = "perspectives", topics = c(16, 18))
cloud(Content, topic = 13, scale = c(2, 0.25))
```
