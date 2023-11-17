# Restrictions for the current version of text recognition

Text recognition is currently working with a number of restrictions. Make sure to consider them before getting started:

* The recognition accuracy (`confidence`) value is only calculated for a `line`. For words and languages, it will be borrowed from the line's value.
* You will see a single language for all recognized words, even though they are recognized in all languages from the supported [model](supported-languages.md). For example, if you specify `["en", " ar"]` and the text has mostly English words, all words will be marked as `en`, including those in Arabic.
* The text recognition feature may have difficulties with:
   * Artistic fonts.
   * Vertical text (when a word is written top to bottom).
   * Forms where each character is typed in a separate cell.
   * Very large font size, e.g., when one word takes up half of the image.

#### What's next {#what-is-next}

* [Try recognizing text in an image](../../operations/ocr/text-detection-image.md)
* [View the list of supported languages and models](supported-languages.md)