- 基于词项与基于全文的搜索
	- Term查询
	  ● Term 是表达语意的最⼩单位。搜索和利⽤统计语⾔模型进⾏⾃然语⾔处理都需要处理 Term
	  ● 特点
	  ● Term Level Query: Term Query / Range Query / Exists Query / Prefix Query /Wildcard Query
	  ● 在 ES 中，Term 查询，对输⼊不做分词。会将输⼊作为⼀个整体，在倒排索引中查找准确的词项，并
	  且使⽤相关度算分公式为每个包含该词项的⽂档进⾏相关度算分 – 例如“Apple Store”
	  ● 可以通过 Constant Score 将查询转换成⼀个 Filtering，避免算分，并利⽤缓存，提⾼性能