---
layout: post
title: Clojure vs Python.
---

(def grammar '{Sentence (Noun-Phrase Verb-Phrase)
               Noun-Phrase (Article Noun)
               Verb-Phrase (Verb Noun-Phrase)
               Article #{the a}
               Noun #{man ball woman table}
               Verb #{hit took saw liked}})

(defn generate [phrase]
  (cond (list? phrase) (flatten (map generate phrase))
        (set? phrase) (generate (rand-nth (seq phrase)))
        (phrase grammar) (generate (phrase grammar))
        (symbol? phrase) (list phrase)))

(generate 'Sentence)
