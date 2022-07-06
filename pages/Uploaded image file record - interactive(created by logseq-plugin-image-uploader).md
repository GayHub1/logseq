- #+BEGIN_QUERY
  {:title "Not uploaded images"
    :query [:find (pull ?b [*])
          :where
          [?b :block/page ?p]
          [?p :block/name ?page_name]
          (not [(clojure.string/includes? ?page_name "created by logseq-plugin-image-uploader")])
          [?b :block/content ?content]
          (not [(clojure.string/includes? ?content "{:title \"Not uploaded images\"")])
          [(clojure.string/includes? ?content "](../assets")]
          [(clojure.string/includes? ?content "![")]
          (or [(clojure.string/includes? ?content ".png)")]
              [(clojure.string/includes? ?content ".jpg)")]
              [(clojure.string/includes? ?content ".jpeg)")]
              [(clojure.string/includes? ?content ".gif)")]
              [(clojure.string/includes? ?content ".tiff)")]
              [(clojure.string/includes? ?content ".tif)")]
              [(clojure.string/includes? ?content ".bmp)")]
              [(clojure.string/includes? ?content ".svg)")]
              [(clojure.string/includes? ?content ".webp)")]
              [(clojure.string/includes? ?content ".PNG)")]
              [(clojure.string/includes? ?content ".JPG)")]
              [(clojure.string/includes? ?content ".JPEG)")]
              [(clojure.string/includes? ?content ".GIF)")]
              [(clojure.string/includes? ?content ".TIGG)")]
              [(clojure.string/includes? ?content ".TIF)")]
              [(clojure.string/includes? ?content ".VMP)")]
              [(clojure.string/includes? ?content ".SVG)")]
              [(clojure.string/includes? ?content ".WEBP)")])
        ]}
  #+END_QUERY
- ![Uploaded by Image Uploder](../assets/image_1653181387784_0.png)
- ![Uploaded by Image Uploder](../assets/image-20220518233406287_1653743572042_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210113220722832_1655734960071_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210113223224391_1655735067010_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210118105401587_1655735145785_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210121235326332_1655735837484_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210123203057130_1655736127058_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210124213009859_1655736165278_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210124215636329_1655736405902_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210321200215772_1655738517692_0.png)
- ![Uploaded by Image Uploder](../assets/image-20210321222317413_1655738537132_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656229689191_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656230499839_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656236821921_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656238639121_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656238759844_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656334192845_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656334924326_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656413128774_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656731662352_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656771663399_0.png)
- ![Uploaded by Image Uploder](../assets/image-20220703005955976_1656782255264_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656782628235_0.png)
- ![Uploaded by Image Uploder](../assets/image_1656783407886_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657115129912_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657116779426_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657116813488_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657116981321_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657117094875_0.png)
- ![Uploaded by Image Uploder](../assets/image_1657117769317_0.png)