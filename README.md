# logseq-default-queries-journals
 - Enhancing Journal Queries turns Logseq into an outliner task management tool.

## Edit config.edn
 - On Logseq, edit config.edn file. Once you've done that, create your own task and try it out.

![Animation1](https://user-images.githubusercontent.com/111847207/200155115-0344d96b-5982-43ed-b3cb-f40107b5b858.gif)

### BEFORE
 - There are only two kinds of queries by default. 
![image](https://user-images.githubusercontent.com/111847207/200147305-6fe68860-faef-4f53-bf42-4c5a11d3330c.png)



### REPLACE

#### Link
 - [Advanced Queries (docs.logseq.com)](https://docs.logseq.com/#/page/advanced%20queries)
 - [Logseq Default Query 6-pack](https://gist.github.com/psu/abf8d8c206f11d56c0e214d0bfcf065f#file-logseq-config-default-queries-edn-L50-L257)
 
#### Try sample code

 Contains the following 7 queries

 1. 🔨 Working Tasks #NOW

 1. 📅 Scheduled to #LATER

 1. ⚠️ OVERDUE

 1. Deadline within 10 days

 1. 🐬 Project #DOING

 1. ⏳ Not Assigned #WAITING

 1. ⏰ Scheduled appointments, 14 days #TODO

```css
 :default-queries{
	 :journals[



{:title "🔨 Working Tasks #NOW"
    :query (task NOW)
    :collapsed? false
 }



{:title "📅  Scheduled to #LATER"
    :query (task LATER)
    :collapsed? false
 }



{:title "⚠️ OVERDUE"
   :query [:find (pull ?block [*])
   :in $ ?start ?today
   :where
   [?block :block/marker ?marker]

   (or
     [?block :block/scheduled ?d]
     [?block :block/deadline ?d])

   [(>= ?d ?start)]
   [(<   ?d ?today)]

   [(contains? #{"NOW" "LATER" "TODO" "DOING" "WAITING"} ?marker)]]
     :inputs [:180d :today]
     :result-transform  (fn [result]
             (sort-by  (fn [d]
             (get d :block/deadline) ) result ))
     :collapsed? true
}



{:title "Deadline within 10 days"
  :query [:find (pull ?block [*])
          :in $ ?start ?next
          :where
           (or
            [?block :block/deadline ?d]
           )
           [(> ?d ?start)]
           [(< ?d ?next)]
         ]
  :inputs [:0d :10d-after]
      :result-transform  (fn [result]
             (sort-by  (fn [h]
             (get h :block/deadline) ) result ))
  :collapsed? false
}



{:title "🐬 Project #DOING"
    :query (task DOING)
    :collapsed? true
 }



{:title "⏳ Not Assigned #WAITING"
    :query (task WAITING)
    :collapsed? true
}



{:title "⏰ Scheduled appointments, 14 days #TODO"
 :query [:find (pull ?block [*])
         :in $ ?start ?next
         :where
         (or
           [?block :block/scheduled ?d]
         )
         [(> ?d ?start)]
         [(< ?d ?next)]
        ]
 :inputs [:0d :14d-after]
      :result-transform  (fn [result]
             (sort-by  (fn [d]
             (get d :block/scheduled) ) result ))
 :collapsed? false
}



    ]}

```

From [Logseq Column Layout plugin](https://github.com/YU000jp/Logseq-column-Layout)

---

<a href="https://www.buymeacoffee.com/yu000japan" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-violet.png" alt="🍌Buy Me A Coffee" style="height: 42px;width: 152px" ></a>
