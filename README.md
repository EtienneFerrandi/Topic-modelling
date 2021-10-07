# Topic-modelling
A topic modelling on Augustine homilies, in particular those who have been reused by Caesarius, using R.

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

Models(poliblogSelect, pch = c(1, 2, 3, 4),
             legend.position = "bottomright")


Content <- stm(out$documents, out$vocab, K = 20,
                       prevalence =~ Auteur, content =~ Auteur,
                       max.em.its = 75, data = out$meta, init.type = "Spectral")

plot(Content, type = "summary", xlim = c(0, 0.3))
plot(Content, type = "perspectives", topics = 10)
plot(Content, type = "perspectives", topics = c(16, 18))
cloud(Content, topic = 13, scale = c(2, 0.25))
