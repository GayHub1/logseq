- #.v-eisenhower-matrix
	- 📅 在做计划事项。
	  query-table:: true
	  #+BEGIN_QUERY
	  {:query [:find (pull ?b [*])
	        :where
	        [?b :block/marker ?marker]
	        (not (not [?b :block/ref-pages ?p]
	        [?p :page/name ?page-name]
	        [(clojure.string/includes? ?page-name "okr")])
	        (not [?b :block/priority ?priority]
	        [(contains? #{"A" "B" "C"} ?priority)]))
	        [(contains? #{"DOING" "NOW"} ?marker)]]
	   }
	  #+END_QUERY
	- 🌞  待做计划事项。
	  query-table:: true
	  #+BEGIN_QUERY
	  {:query [:find (pull ?b [*])
	        :where
	        [?b :block/marker ?marker]
	        (not (not [?b :block/ref-pages ?p]
	        [?p :page/name ?page-name]
	        [(clojure.string/includes? ?page-name "okr")])
	        (not [?b :block/priority ?priority]
	        [(contains? #{"A" "B" "C"} ?priority)]))
	        [(contains? #{"TODO" "LATER"} ?marker)]]
	   }
	  #+END_QUERY
	- ⏰ 在做其他事项。
	  query-table:: true
	  #+BEGIN_QUERY
	  {:query [:find (pull ?b [*])
	        :where
	        [?b :block/marker ?marker]
	        (not [?b :block/ref-pages ?p]
	        [?p :page/name ?page-name]
	        [(clojure.string/includes? ?page-name "okr")])
	        (not [?b :block/priority ?priority]
	        [(contains? #{"A" "B" "C"} ?priority)])
	        [(contains? #{"DOING" "NOW"} ?marker)]]
	   }
	  #+END_QUERY
	- 🚮  待做其他事项。
	  query-table:: true
	  #+BEGIN_QUERY
	  {:query [:find (pull ?b [*])
	        :where
	        [?b :block/marker ?marker]
	        (not [?b :block/ref-pages ?p]
	        [?p :page/name ?page-name]
	        [(clojure.string/includes? ?page-name "okr")])
	        (not [?b :block/priority ?priority]
	        [(contains? #{"A" "B" "C"} ?priority)])
	        [(contains? #{"LATER"} ?marker)]]
	   }
	  #+END_QUERY
-