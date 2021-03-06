<div tabindex="-1" id="notebook" class="border-box-sizing">

<div class="container" id="notebook-container">

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

# TV Script Generation[¶](#TV-Script-Generation)

In this project, you'll generate your own [Simpsons](https://en.wikipedia.org/wiki/The_Simpsons) TV scripts using RNNs. You'll be using part of the [Simpsons dataset](https://www.kaggle.com/wcukierski/the-simpsons-by-the-data) of scripts from 27 seasons. The Neural Network you'll build will generate a new TV script for a scene at [Moe's Tavern](https://simpsonswiki.com/wiki/Moe's_Tavern).

## Get the Data[¶](#Get-the-Data)

The data is already provided for you. You'll be using a subset of the original dataset. It consists of only the scenes in Moe's Tavern. This doesn't include other versions of the tavern, like "Moe's Cavern", "Flaming Moe's", "Uncle Moe's Family Feed-Bag", etc..

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [1]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">import</span> <span class="nn">helper</span>

<span class="n">data_dir</span> <span class="o">=</span> <span class="s1">'./data/simpsons/moes_tavern_lines.txt'</span>
<span class="n">text</span> <span class="o">=</span> <span class="n">helper</span><span class="o">.</span><span class="n">load_data</span><span class="p">(</span><span class="n">data_dir</span><span class="p">)</span>
<span class="c1"># Ignore notice, since we don't use it for analysing the data</span>
<span class="n">text</span> <span class="o">=</span> <span class="n">text</span><span class="p">[</span><span class="mi">81</span><span class="p">:]</span>
<span class="nb">print</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">text</span><span class="p">))</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>305203
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Explore the Data[¶](#Explore-the-Data)

Play around with `view_sentence_range` to view different parts of the data.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [2]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="n">view_sentence_range</span> <span class="o">=</span> <span class="p">(</span><span class="mi">0</span><span class="p">,</span> <span class="mi">20</span><span class="p">)</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>

<span class="nb">print</span><span class="p">(</span><span class="s1">'Dataset Stats'</span><span class="p">)</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'Roughly the number of unique words:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="nb">len</span><span class="p">({</span><span class="n">word</span><span class="p">:</span> <span class="kc">None</span> <span class="k">for</span> <span class="n">word</span> <span class="ow">in</span> <span class="n">text</span><span class="o">.</span><span class="n">split</span><span class="p">()})))</span>
<span class="n">scenes</span> <span class="o">=</span> <span class="n">text</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">'</span><span class="se">\n\n</span><span class="s1">'</span><span class="p">)</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'Number of scenes:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">scenes</span><span class="p">)))</span>
<span class="n">sentence_count_scene</span> <span class="o">=</span> <span class="p">[</span><span class="n">scene</span><span class="o">.</span><span class="n">count</span><span class="p">(</span><span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="p">)</span> <span class="k">for</span> <span class="n">scene</span> <span class="ow">in</span> <span class="n">scenes</span><span class="p">]</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'Average number of sentences in each scene:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">np</span><span class="o">.</span><span class="n">average</span><span class="p">(</span><span class="n">sentence_count_scene</span><span class="p">)))</span>

<span class="n">sentences</span> <span class="o">=</span> <span class="p">[</span><span class="n">sentence</span> <span class="k">for</span> <span class="n">scene</span> <span class="ow">in</span> <span class="n">scenes</span> <span class="k">for</span> <span class="n">sentence</span> <span class="ow">in</span> <span class="n">scene</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="p">)]</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'Number of lines:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">sentences</span><span class="p">)))</span>
<span class="n">word_count_sentence</span> <span class="o">=</span> <span class="p">[</span><span class="nb">len</span><span class="p">(</span><span class="n">sentence</span><span class="o">.</span><span class="n">split</span><span class="p">())</span> <span class="k">for</span> <span class="n">sentence</span> <span class="ow">in</span> <span class="n">sentences</span><span class="p">]</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'Average number of words in each line:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">np</span><span class="o">.</span><span class="n">average</span><span class="p">(</span><span class="n">word_count_sentence</span><span class="p">)))</span>

<span class="nb">print</span><span class="p">()</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'The sentences</span> <span class="si">{}</span> <span class="s1">to</span> <span class="si">{}</span><span class="s1">:'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="o">*</span><span class="n">view_sentence_range</span><span class="p">))</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="n">text</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="p">)[</span><span class="n">view_sentence_range</span><span class="p">[</span><span class="mi">0</span><span class="p">]:</span><span class="n">view_sentence_range</span><span class="p">[</span><span class="mi">1</span><span class="p">]]))</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Dataset Stats
Roughly the number of unique words: 11492
Number of scenes: 262
Average number of sentences in each scene: 15.251908396946565
Number of lines: 4258
Average number of words in each line: 11.50164396430249

The sentences 0 to 20:

Moe_Szyslak: (INTO PHONE) Moe's Tavern. Where the elite meet to drink.
Bart_Simpson: Eh, yeah, hello, is Mike there? Last name, Rotch.
Moe_Szyslak: (INTO PHONE) Hold on, I'll check. (TO BARFLIES) Mike Rotch. Mike Rotch. Hey, has anybody seen Mike Rotch, lately?
Moe_Szyslak: (INTO PHONE) Listen you little puke. One of these days I'm gonna catch you, and I'm gonna carve my name on your back with an ice pick.
Moe_Szyslak: What's the matter Homer? You're not your normal effervescent self.
Homer_Simpson: I got my problems, Moe. Give me another one.
Moe_Szyslak: Homer, hey, you should not drink to forget your problems.
Barney_Gumble: Yeah, you should only drink to enhance your social skills.

Moe_Szyslak: Ah, isn't that nice. Now, there is a politician who cares.
Barney_Gumble: If I ever vote, it'll be for him. (BELCH)

Barney_Gumble: Hey Homer, how's your neighbor's store doing?
Homer_Simpson: Lousy. He just sits there all day. He'd have a great job if he didn't own the place. (CHUCKLES)
Moe_Szyslak: (STRUGGLING WITH CORKSCREW) Crummy right-handed corkscrews! What does he sell?
Homer_Simpson: Uh, well actually, Moe...
HOMER_(CONT'D: I dunno.
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Implement Preprocessing Functions[¶](#Implement-Preprocessing-Functions)

The first thing to do to any dataset is preprocessing. Implement the following preprocessing functions below:

*   Lookup Table
*   Tokenize Punctuation

### Lookup Table[¶](#Lookup-Table)

To create a word embedding, you first need to transform the words to ids. In this function, create two dictionaries:

*   Dictionary to go from the words to an id, we'll call `vocab_to_int`
*   Dictionary to go from the id to word, we'll call `int_to_vocab`

Return these dictionaries in the following tuple `(vocab_to_int, int_to_vocab)`

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [3]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>
<span class="kn">import</span> <span class="nn">problem_unittests</span> <span class="k">as</span> <span class="nn">tests</span>

<span class="k">def</span> <span class="nf">create_lookup_tables</span><span class="p">(</span><span class="n">text</span><span class="p">):</span>

    <span class="n">vocab</span> <span class="o">=</span> <span class="nb">set</span><span class="p">(</span><span class="n">text</span><span class="p">)</span>
    <span class="n">vocab_to_int</span> <span class="o">=</span> <span class="p">{</span><span class="n">c</span><span class="p">:</span> <span class="n">i</span> <span class="k">for</span> <span class="n">i</span><span class="p">,</span> <span class="n">c</span> <span class="ow">in</span> <span class="nb">enumerate</span><span class="p">(</span><span class="n">vocab</span><span class="p">)}</span>
    <span class="n">int_to_vocab</span> <span class="o">=</span> <span class="nb">dict</span><span class="p">(</span><span class="nb">enumerate</span><span class="p">(</span><span class="n">vocab</span><span class="p">))</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">int_to_vocab</span><span class="p">)</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">vocab_to_int</span><span class="p">)</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">vocab_to_int</span><span class="p">,</span> <span class="n">int_to_vocab</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_create_lookup_tables</span><span class="p">(</span><span class="n">create_lookup_tables</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>{0: "moe's", 1: 'yeah', 2: 'gonna', 3: 'mike', 4: 'your', 5: 'drink', 6: 'there', 7: 'has', 8: 'effervescent', 9: 'you', 10: 'an', 11: 'bart_simpson', 12: 'where', 13: 'i', 14: 'and', 15: 'problems', 16: 'skills', 17: 'the', 18: 'hello', 19: 'moe', 20: 'little', 21: 'forget', 22: 'homer', 23: 'should', 24: 'back', 25: 'got', 26: 'moe_szyslak', 27: 'self', 28: 'matter', 29: 'days', 30: 'one', 31: 'elite', 32: 'not', 33: 'is', 34: 'on', 35: 'barney_gumble', 36: 'check', 37: 'hey', 38: 'to', 39: 'meet', 40: 'pick', 41: 'of', 42: "i'll", 43: 'normal', 44: 'enhance', 45: 'eh', 46: 'seen', 47: 'these', 48: 'give', 49: 'only', 50: 'catch', 51: 'listen', 52: 'rotch', 53: 'carve', 54: 'ice', 55: 'anybody', 56: 'with', 57: 'last', 58: "i'm", 59: 'social', 60: 'tavern', 61: 'me', 62: 'whats', 63: 'my', 64: 'homer_simpson', 65: 'name', 66: 'hold', 67: 'lately', 68: "you're", 69: 'puke', 70: 'another'}
{"moe's": 0, 'yeah': 1, 'mike': 3, 'gonna': 2, 'has': 7, 'effervescent': 8, 'you': 9, "i'm": 58, 'bart_simpson': 11, 'where': 12, 'problems': 15, 'and': 14, 'skills': 16, 'the': 17, 'hello': 18, 'moe': 19, 'homer_simpson': 64, 'little': 20, 'forget': 21, 'homer': 22, 'should': 23, 'got': 25, 'moe_szyslak': 26, 'self': 27, 'matter': 28, 'there': 6, 'days': 29, 'one': 30, 'elite': 31, 'not': 32, 'is': 33, 'on': 34, 'barney_gumble': 35, 'ice': 54, 'hey': 37, 'to': 38, 'meet': 39, 'pick': 40, 'an': 10, 'of': 41, "i'll": 42, 'drink': 5, 'enhance': 44, 'eh': 45, 'seen': 46, 'these': 47, 'give': 48, 'normal': 43, 'catch': 50, 'listen': 51, 'rotch': 52, 'carve': 53, 'tavern': 60, 'anybody': 55, 'with': 56, 'last': 57, 'social': 59, 'check': 36, 'only': 49, 'me': 61, 'whats': 62, 'back': 24, 'my': 63, 'lately': 67, 'name': 65, 'i': 13, 'hold': 66, 'your': 4, "you're": 68, 'puke': 69, 'another': 70}
Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Tokenize Punctuation[¶](#Tokenize-Punctuation)

We'll be splitting the script into a word array using spaces as delimiters. However, punctuations like periods and exclamation marks make it hard for the neural network to distinguish between the word "bye" and "bye!".

Implement the function `token_lookup` to return a dict that will be used to tokenize symbols like "!" into "||Exclamation_Mark||". Create a dictionary for the following symbols where the symbol is the key and value is the token:

*   Period ( . )
*   Comma ( , )
*   Quotation Mark ( " )
*   Semicolon ( ; )
*   Exclamation mark ( ! )
*   Question mark ( ? )
*   Left Parentheses ( ( )
*   Right Parentheses ( ) )
*   Dash ( -- )
*   Return ( \n )

This dictionary will be used to token the symbols and add the delimiter (space) around it. This separates the symbols as it's own word, making it easier for the neural network to predict on the next word. Make sure you don't use a token that could be confused as a word. Instead of using the token "dash", try using something like "||dash||".

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [4]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">token_lookup</span><span class="p">():</span>
    <span class="sd">"""</span>
 <span class="sd">Generate a dict to turn punctuation into a token.</span>
 <span class="sd">:return: Tokenize dictionary where the key is the punctuation and the value is the token</span>
 <span class="sd">"""</span>
    <span class="c1">#text = text.lower()</span>
    <span class="n">diction</span> <span class="o">=</span> <span class="p">{</span><span class="s2">"."</span><span class="p">:</span> <span class="s2">"||PERIOD||"</span><span class="p">,</span><span class="s2">","</span><span class="p">:</span> <span class="s2">"||COMMA||"</span><span class="p">,</span><span class="s1">'"'</span><span class="p">:</span> <span class="s2">"||QUOTATION_MARK||"</span><span class="p">,</span><span class="s2">";"</span><span class="p">:</span> <span class="s2">"||SEMICOLON||"</span><span class="p">,</span><span class="s2">"!"</span><span class="p">:</span> <span class="s2">"||EXCLAMATION_MARK||"</span><span class="p">,</span>
            <span class="s2">"?"</span><span class="p">:</span> <span class="s2">"||QUESTION_MARK||"</span><span class="p">,</span><span class="s2">"("</span><span class="p">:</span> <span class="s2">"||LEFT_PAREN||"</span><span class="p">,</span><span class="s2">")"</span><span class="p">:</span> <span class="s2">"||RIGHT_PAREN||"</span><span class="p">,</span><span class="s2">"--"</span><span class="p">:</span> <span class="s2">"||DASH||"</span><span class="p">,</span>
            <span class="s2">"</span><span class="se">\n</span><span class="s2">"</span><span class="p">:</span> <span class="s2">"||NEW_LINE||"</span><span class="p">}</span>

    <span class="c1"># TODO: Implement Function</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">diction</span><span class="p">)</span>
    <span class="k">return</span> <span class="n">diction</span>
<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_tokenize</span><span class="p">(</span><span class="n">token_lookup</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>{';': '||SEMICOLON||', ',': '||COMMA||', '.': '||PERIOD||', '!': '||EXCLAMATION_MARK||', ')': '||RIGHT_PAREN||', '?': '||QUESTION_MARK||', '(': '||LEFT_PAREN||', '"': '||QUOTATION_MARK||', '--': '||DASH||', '\n': '||NEW_LINE||'}
Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Preprocess all the data and save it[¶](#Preprocess-all-the-data-and-save-it)

Running the code cell below will preprocess all the data and save it to file.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [5]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="c1"># Preprocess Training, Validation, and Testing Data</span>
<span class="n">helper</span><span class="o">.</span><span class="n">preprocess_and_save_data</span><span class="p">(</span><span class="n">data_dir</span><span class="p">,</span> <span class="n">token_lookup</span><span class="p">,</span> <span class="n">create_lookup_tables</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>{';': '||SEMICOLON||', ',': '||COMMA||', '.': '||PERIOD||', '!': '||EXCLAMATION_MARK||', ')': '||RIGHT_PAREN||', '?': '||QUESTION_MARK||', '(': '||LEFT_PAREN||', '"': '||QUOTATION_MARK||', '--': '||DASH||', '\n': '||NEW_LINE||'}
{0: 'sweetly', 1: 'suave', 2: 'compliment', 3: 'escort', 4: 'add', 5: 'easter', 6: 'her', 7: 'inherent', 8: 'dreamy', 9: '_burns_heads:', 10: 'full-blooded', 11: 'by', 12: 'wolfcastle', 13: 'shaker', 14: 'dingy', 15: 'east', 16: 'playoff', 17: 'bitterly', 18: 'officer', 19: 'kearney_zzyzwicz:', 20: 'wrong', 21: 'mad', 22: 'looking', 23: 'anxious', 24: 'wreck', 25: 'drank', 26: "'bout", 27: 'teenage_barney:', 28: 'unlucky', 29: 'another', 30: 'dealer', 31: 'courthouse', 32: 'ninety-nine', 33: 'writers', 34: 'sedaris', 35: 'forty-nine', 36: "can't-believe-how-bald-he-is", 37: 'admirer', 38: 'forgive', 39: 'joining', 40: 'runs', 41: 'tons', 42: 'knees', 43: 'kind', 44: 'severe', 45: 'benjamin:', 46: 'onions', 47: 'buttons', 48: 'babar', 49: 'kucinich', 50: 'factor', 51: 'musses', 52: 'op', 53: 'thighs', 54: 'beatings', 55: 'banquo', 56: 'tomahto', 57: "pickin'", 58: 'explaining', 59: 'rug', 60: 'manjula', 61: 'fatso', 62: 'intense', 63: 'sizes', 64: 'spanish', 65: 'dollars', 66: "tester's", 67: 'this:', 68: 'shortcomings', 69: 'tiny', 70: 'shelbyville', 71: 'feeling', 72: 'pouring', 73: 'furiously', 74: 'sitting', 75: 'youth', 76: 'shoes', 77: 'ugliness', 78: 'softer', 79: 'sooo', 80: 'snail', 81: 'rob', 82: 'event', 83: 'low-life', 84: "y'money's", 85: 'she-pu', 86: 'read', 87: 'fulla', 88: 'p', 89: 'sesame', 90: 'anyone', 91: 'hmf', 92: 's', 93: 'is', 94: 'moonshine', 95: 'grains', 96: 'insist', 97: 'ever', 98: 'are', 99: 'depression', 100: 'exchange', 101: 'gee', 102: 'rebuilt', 103: 'superdad', 104: 'crotch', 105: 'hustle', 106: 'sees/', 107: 'hibbert', 108: 'accurate', 109: 'tiger', 110: 'putting', 111: 'hurry', 112: 'pasta', 113: 'cheat', 114: 'sickens', 115: 'sight-unseen', 116: 'generally', 117: 'photo', 118: 'ugliest', 119: 'harvard', 120: 'paint', 121: 'hyper-credits', 122: 'elephants', 123: 'in', 124: 'lemme', 125: 'falling', 126: 'strictly', 127: 'administration', 128: 'slobs', 129: 'helpless', 130: 'trenchant', 131: 'lindsay_naegle:', 132: 'ready', 133: 'went', 134: 'continuum', 135: 'plus', 136: 'do', 137: 'excitement', 138: 'old-time', 139: 'chipped', 140: 'win', 141: 'tick', 142: 'letters', 143: 'rest', 144: 'maggie', 145: 'bide', 146: 'pontiff', 147: "now's", 148: 'random', 149: 'shred', 150: 'ails', 151: 'wind', 152: "toot's", 153: 'frightened', 154: 'coherent', 155: 'drug', 156: 'deer', 157: 'envy-tations', 158: 'tape', 159: 'quarry', 160: 'flash', 161: 'cecil', 162: 'tonight', 163: 'composite', 164: "guy's", 165: 'whistling', 166: 'switched', 167: "'em", 168: "sippin'", 169: 'decision', 170: 'lobster', 171: 'forced', 172: 'opening', 173: 'show-off', 174: 'phone', 175: 'stop', 176: 'committee', 177: 'camera', 178: 'looting', 179: 'stamp', 180: 'fabulous', 181: 'big', 182: 'premiering', 183: 'earth', 184: 'champ', 185: 'drollery', 186: 'ding-a-ding-ding-ding-ding-ding-ding', 187: 'endorsed', 188: 'flag', 189: "betsy'll", 190: 'folks', 191: 'further', 192: 'though', 193: 'cheated', 194: 'upon', 195: 'oof', 196: 'whoa', 197: "life's", 198: 'patriotic', 199: 'befouled', 200: 'owner', 201: 'menlo', 202: 'holding', 203: 'trivia', 204: 'depressant', 205: 'horrors', 206: 'cushions', 207: 'wikipedia', 208: 'couch', 209: 'neighbor', 210: 'advice', 211: 'musical', 212: "floatin'", 213: 'soaps', 214: 'city', 215: 'illegal', 216: "lenny's", 217: 'talkative', 218: 'frog', 219: 'passed', 220: 'moolah-stealing', 221: 'neat', 222: 'fausto', 223: 'beats', 224: 're-al', 225: 'freely', 226: 'ruin', 227: 'men', 228: 'represent', 229: 'when-i-get-a-hold-of-you', 230: 'maximum', 231: 'rem', 232: 'chapel', 233: 'woulda', 234: 'wood', 235: 'presided', 236: 'north', 237: 'barney_gumble:', 238: "wouldn't", 239: 'glee', 240: 'chunky', 241: "o'problem", 242: "tab's", 243: 'tornado', 244: 'strolled', 245: 'wooden', 246: "cat's", 247: 'gregor', 248: 'inserted', 249: 'lifestyle', 250: 'eyeball', 251: 'finally', 252: 'dejected_barfly:', 253: 'encores', 254: 'difficult', 255: 'caholic', 256: 'chuckle', 257: 'beam', 258: 'kim_basinger:', 259: 'approval', 260: 'proof', 261: 'starve', 262: 'refreshingness', 263: 'lorre', 264: 'dramatic', 265: 'yard', 266: 'therefore', 267: 'disco', 268: 'space', 269: 'refill', 270: 'gold', 271: 'delicately', 272: 'whatchamacallit', 273: 'arguing', 274: 'sex', 275: 'ordered', 276: 'majority', 277: 'urban', 278: 'achebe', 279: 'astrid', 280: 'suing', 281: 'manboobs', 282: 'nursemaid', 283: 'balls', 284: 'shreda', 285: 'brunswick', 286: 'dignified', 287: "ball's", 288: 'batmobile', 289: 'el', 290: 'flophouse', 291: 'as', 292: 'tv_announcer:', 293: 'tab', 294: 'who-o-oa', 295: 'enjoy', 296: 'slit', 297: 'brick', 298: 'wondering', 299: 'sponge', 300: 'lovers', 301: 'incriminating', 302: 'savagely', 303: 'slurps', 304: 'eva', 305: 'pyramid', 306: 'saw', 307: "men's", 308: 'genius', 309: 'prep', 310: 'o', 311: 'ees', 312: 'cannoli', 313: 'confident', 314: 'fella', 315: 'barber', 316: 'itchy', 317: 'runt', 318: 'snapping', 319: 'intoxicated', 320: 'probably', 321: 'andrew', 322: 'anywhere', 323: 'forward', 324: 'clearly', 325: 'loser', 326: 'slapped', 327: 'producers', 328: 'politician', 329: 'groveling', 330: 'making', 331: 'wednesday', 332: 'smithers', 333: 'kirk_van_houten:', 334: 'choked', 335: 'breakfast', 336: 'form', 337: 'flashing', 338: 'dammit', 339: 'like', 340: 'smoker', 341: 'pal', 342: "homer'll", 343: 'forever', 344: 'trustworthy', 345: 'cruise', 346: 'traditions', 347: 'expert', 348: 'bed', 349: 'stinks', 350: "children's", 351: 'dumbest', 352: 'different', 353: 'continued', 354: 'brakes', 355: 'color', 356: 'england', 357: 'evasive', 358: 'ointment', 359: 'before', 360: 'montrer', 361: 'icelandic', 362: "collector's", 363: 'too', 364: 'children', 365: 'weird', 366: 'urine', 367: 'kissed', 368: 'along', 369: 'furniture', 370: 'rump', 371: 'impressed', 372: 'putty', 373: 'shall', 374: 'thought', 375: 'and', 376: 'frenchman', 377: "don'tcha", 378: 'hospital', 379: 'invented', 380: 'eggs', 381: 'far', 382: 'rainier', 383: 'jokes', 384: "snappin'", 385: 'typing', 386: 'wad', 387: 'goodnight', 388: 'kay', 389: 'total', 390: 'each', 391: 'forehead', 392: 'covering', 393: 'murdoch', 394: 'feet', 395: 'newspaper', 396: "neat's-foot", 397: 'bust', 398: 'supplying', 399: 'blackjack', 400: 'marguerite:', 401: 'martini', 402: 'ehhhhhh', 403: 'stays', 404: 'stars', 405: 'gentles', 406: 'glove', 407: 'incredible', 408: 'kisser', 409: 'apron', 410: 'young', 411: 'book_club_member:', 412: 'arm', 413: 'helped', 414: 'sighs', 415: 'burt_reynolds:', 416: 'stalking', 417: 'windowshade', 418: 'kinda', 419: 'frustrated', 420: 'barter', 421: 'away', 422: 'chinese_restaurateur:', 423: 'alva', 424: 'players', 425: 'trees', 426: 'terrific', 427: 'occupancy', 428: 'salt', 429: 'pit', 430: 'puke', 431: 'saget', 432: 'demo', 433: 'roach', 434: 'whose', 435: 'somebody', 436: 'sober', 437: 'overhearing', 438: 'roz', 439: 'why', 440: 'cheryl', 441: "'er", 442: 'swear', 443: 'oww', 444: 'belches', 445: 'disappointment', 446: 'successful', 447: 'clown', 448: 'playhouse', 449: 'clinton', 450: 'danish', 451: 'yea', 452: 'growing', 453: 'babies', 454: 'scam', 455: 'booger', 456: 'guiltily', 457: 'carb', 458: 'brawled', 459: 'domestic', 460: 'earpiece', 461: 'plans', 462: 'wound', 463: 'coincidentally', 464: 'clipped', 465: 'here', 466: 'aboard', 467: 'look', 468: 'quick-like', 469: 'message', 470: 'meals', 471: 'tyson/secretariat', 472: 'lard', 473: 'men:', 474: 'brilliant', 475: 'roy', 476: 'ineffective', 477: 'nauseous', 478: 'mayor_joe_quimby:', 479: 'dinner', 480: 'results', 481: 'anonymous', 482: 'bar', 483: 'homesick', 484: 'forgot', 485: 'sounds', 486: "c'mon", 487: 'sideshow_mel:', 488: 'wish-meat', 489: 'hundreds', 490: 'madman', 491: 'shoot', 492: 'grade', 493: 'counterfeit', 494: 'shyly', 495: 'many', 496: 'forbidden', 497: 'rascals', 498: 'pudgy', 499: 'portentous', 500: 'drown', 501: 'launch', 502: 'saving', 503: "'topes", 504: 'santa', 505: 'fat-free', 506: 'find', 507: 'palm', 508: 'pointing', 509: 'get', 510: 'noble', 511: 'steak', 512: 'true', 513: "other's", 514: 'wussy', 515: 'always', 516: 'whole', 517: 'consulting', 518: "treatin'", 519: 'increased', 520: 'dr', 521: 'next', 522: 'doy', 523: 'marge_simpson:', 524: 'wants', 525: 'appreciate', 526: 'acting', 527: 'additional-seating-capacity', 528: 'positive', 529: 'argue', 530: 'wudgy', 531: 'costume', 532: 'twenty-nine', 533: 'purse', 534: 'german', 535: "rentin'", 536: 'medical', 537: 'wore', 538: 'aging', 539: 'poem', 540: 'election', 541: 'teenage', 542: 'poorer', 543: 'huge', 544: 'friction', 545: 'officials', 546: 'heals', 547: "time's", 548: 'uniforms', 549: 'mugs', 550: 'cesss', 551: 'lowest', 552: "barney's", 553: 'freak', 554: 'kirk_voice_milhouse:', 555: 'disposal', 556: 'sister', 557: 'politicians', 558: 'attraction', 559: '4x4', 560: 'amanda', 561: 'bulldozing', 562: "isn't", 563: 'seductive', 564: 'endorsement', 565: 'crowbar', 566: 'ahem', 567: 'unless', 568: 'heading', 569: 'distract', 570: "school's", 571: 'working', 572: 'four-drink', 573: 'fortune', 574: 'donor', 575: 'sentimonies', 576: 'shape', 577: 'sen', 578: 'boozebag', 579: 'umm', 580: 'hooters', 581: "tv's", 582: 'pressure', 583: 'done:', 584: 'take', 585: 'gin-slingers', 586: 'contented', 587: 'comes', 588: 'computer', 589: 'okay', 590: 'palmerston', 591: 'smile', 592: 'stirring', 593: "stallin'", 594: "tv'll", 595: 'poker', 596: 'became', 597: 'diddilies', 598: 'whatchacallit', 599: 'real', 600: 'arts', 601: 'problem', 602: 'dreams', 603: 'cuckoo', 604: 'bid', 605: 'gasoline', 606: 'head', 607: 'rookie', 608: 'daaaaad', 609: 'billiard', 610: 'and:', 611: 'squeal', 612: 'ehhh', 613: 'nonchalant', 614: 'neither', 615: 'channel', 616: 'chuckling', 617: 'lessee', 618: 'crimes', 619: 'blob', 620: 'sideshow_bob:', 621: 'embarrassing', 622: 'geyser', 623: 'hollywood', 624: 'dejected', 625: 'byrne', 626: 'pub', 627: 'jazz', 628: 'few', 629: 'reads', 630: 'compadre', 631: 'choose', 632: 'semi-imported', 633: 'milks', 634: 'worldview', 635: 'yee-haw', 636: 'neighbors', 637: 'koji', 638: 'excavating', 639: 'privacy', 640: 'nap', 641: 'frat', 642: 'banquet', 643: "we've", 644: 'fevered', 645: 'slim', 646: 'frazier', 647: 'woodchucks', 648: 'ehhhhhhhhh', 649: 'drunkenly', 650: 'arrested:', 651: 'toy', 652: 'dress', 653: 'known', 654: 'encore', 655: 'deal', 656: 'bones', 657: 'kiss', 658: 'words', 659: 'advantage', 660: 'appreciated', 661: 'dark', 662: 'carpet', 663: 'church', 664: 'believe', 665: 'goodwill', 666: 'uses', 667: 'firing', 668: 'leathery', 669: 'squad', 670: "speakin'", 671: 'whispers', 672: 'investor', 673: 'jigger', 674: 'disillusioned', 675: 'young_moe:', 676: 'planted', 677: 'thumb', 678: "soundin'", 679: 'apu', 680: 'savings', 681: "scammin'", 682: 'troy_mcclure:', 683: 'bridge', 684: 'toms', 685: "industry's", 686: 'brother-in-law', 687: 'bets', 688: 'treehouse', 689: 'buddies', 690: 'tease', 691: 'started', 692: 'betty:', 693: 'alma', 694: 'hotel', 695: 'beings', 696: 'eye-gouger', 697: "shouldn't", 698: 'kansas', 699: "liftin'", 700: 'multi-purpose', 701: 'kramer', 702: 'bellyaching', 703: 'inspector', 704: 'cake', 705: 'dipping', 706: 'microwave', 707: 'chairman', 708: 'punishment', 709: 'nameless', 710: 'backward', 711: 'mild', 712: 'deliberately', 713: 'ale', 714: 'smallest', 715: 'everywhere', 716: 'drummer', 717: 'riveting', 718: 'milk', 719: 'moe-clone:', 720: 'looks', 721: 'heavyset', 722: 'motorcycle', 723: 'tow-joes', 724: "tryin'", 725: 'flowers', 726: 'half-back', 727: 'given', 728: 'career', 729: 'sniper', 730: 'brusque', 731: 'halloween', 732: 'yuh-huh', 733: 'ninety-eight', 734: 'lenses', 735: 'expensive', 736: 'shuts', 737: 'reporter:', 738: 'museum', 739: 'specified', 740: 'shirt', 741: 'burps', 742: 'spouses', 743: 'napkins', 744: 'marvelous', 745: 'man:', 746: "swishifyin'", 747: 'hillbillies', 748: 'cheering', 749: 'rat', 750: 'shipment', 751: 'anymore', 752: '70', 753: 'me', 754: 'quimby', 755: 'everyday', 756: 'tidy', 757: 'alphabet', 758: 'agree', 759: 'struggling', 760: 'er', 761: 'supermodel', 762: 'jams', 763: 'hangs', 764: 'swimmers', 765: 'boxcars', 766: 'homeless', 767: 'angrily', 768: 'traitors', 769: 'massive', 770: 'jewelry', 771: 'dory', 772: 'hollye', 773: "dolph's_dad:", 774: 'fired', 775: 'tips', 776: 'broad', 777: 'happily:', 778: 'entire', 779: "i'd'a", 780: 'enjoyed', 781: 'shorter', 782: 'sucker', 783: "let's", 784: 'occurrence', 785: 'awed', 786: 'peanuts', 787: 'lily-pond', 788: 'presses', 789: "ma's", 790: 'tv_father:', 791: 'actually', 792: 'stripe', 793: 'apu_nahasapeemapetilon:', 794: 'unusual', 795: "g'ahead", 796: 'leans', 797: 'whatsit', 798: 'eighty-six', 799: 'incognito', 800: 'plug', 801: 'director', 802: 'remodel', 803: 'coy', 804: 'oak', 805: "comin'", 806: 'decency', 807: 'afternoon', 808: 'formico', 809: 'crying', 810: 'has', 811: 'vengeful', 812: 'competing', 813: 'businessman_#1:', 814: 'contemporary', 815: 'ding-a-ding-ding-a-ding-ding', 816: 'rolling', 817: 'legoland', 818: "clancy's", 819: 'build', 820: 'stayed', 821: 'drinker', 822: 'partner', 823: 'stocking', 824: 'romance', 825: 'kemi:', 826: 'principles', 827: 'pour', 828: 'alley', 829: "leavin'", 830: 'pigtown', 831: 'mumbling', 832: 'extract', 833: 'seymour_skinner:', 834: 'wise', 835: "gentleman's", 836: 'hank_williams_jr', 837: 'least', 838: 'phasing', 839: 'steam', 840: 'carolina', 841: 'johnny_carson:', 842: 'born', 843: 'depository', 844: "aren'tcha", 845: 'pipes', 846: 'hubub', 847: 'knuckles', 848: 'bars', 849: 'seminar', 850: 'eighty-seven', 851: 'lead', 852: 'bret:', 853: 'ruuuule', 854: 'population', 855: 'halvsies', 856: 'syndicate', 857: 'agh', 858: 'sitar', 859: 'whaddaya', 860: 'heroism', 861: 'continuing', 862: 'side:', 863: 'mention', 864: 'arimasen', 865: 'unfair', 866: 'anderson', 867: 'wear', 868: 'bottomless', 869: 'trying', 870: 'grienke', 871: 'expense', 872: 'stay', 873: 'mommy', 874: 'mines', 875: 'paintings', 876: 'lipo', 877: 'warranty', 878: 'housework', 879: 'insulted', 880: 'leak', 881: 'klingon', 882: 'carmichael', 883: 'express', 884: 'freaky', 885: 'unlike', 886: 'touchdown', 887: 'streetlights', 888: 'declared', 889: 'szyslak', 890: 'sledge-hammer', 891: 'hiya', 892: 'carey', 893: 'camp', 894: 'scrubbing', 895: 'tolerance', 896: 'animals', 897: 'rev', 898: 'connor', 899: 'seek', 900: 'mountain', 901: 'strips', 902: 'helen', 903: 'crowned', 904: 'lonely', 905: 'wing', 906: 'the', 907: 'joey_kramer:', 908: 'barney-type', 909: "hadn't", 910: 'high-definition', 911: 'pus-bucket', 912: 'answering', 913: 'punk', 914: 'fustigate', 915: 'european', 916: 'synthesize', 917: 'scanning', 918: 'blur', 919: 'ron_howard:', 920: 'certainly', 921: 'would', 922: 'cheap', 923: 'sweaty', 924: 'puzzled', 925: 'explanation', 926: 'issuing', 927: 'crowd', 928: 'sagely', 929: 'employees', 930: 'accept', 931: 'viva', 932: 'fills', 933: 'seconds', 934: 'filthy', 935: 'kennedy', 936: 'cell', 937: 'photographer', 938: 'give', 939: 'surgery', 940: 'wallet', 941: 'reasons', 942: 'explain', 943: 'homer_simpson:', 944: "'round", 945: 'from', 946: "nothin's", 947: 'mystery', 948: 'chain', 949: 'milhouse', 950: 'bottles', 951: 'meanwhile', 952: 'clubs', 953: 'itself', 954: 'longer', 955: 'territorial', 956: 'ask', 957: 'awe', 958: 'jury', 959: 'bonding', 960: 'swamp', 961: 'super-nice', 962: 'coast', 963: 'dumb', 964: 'th', 965: 'jacques:', 966: 'ends', 967: 'yew', 968: 'encouraging', 969: 'sketching', 970: 'sickened', 971: 'sperm', 972: "fendin'", 973: 'r', 974: 'jesus', 975: 'mellow', 976: 'chipper', 977: 'hmmmm', 978: 'swe-ee-ee-ee-eet', 979: 'nickel', 980: 'interested', 981: 'insulin', 982: 'hounds', 983: 'alien', 984: 'ditched', 985: '||comma||', 986: 'wiping', 987: '_babcock:', 988: 'song', 989: 'magic', 990: 'buzz', 991: 'paramedic:', 992: 'muertos', 993: 'lame', 994: 'astonishment', 995: 'rub', 996: 'box', 997: 'attach', 998: 'open', 999: 'comedy', 1000: 'announcer:', 1001: 'wiggle-frowns', 1002: 'eminence', 1003: 'except', 1004: 'coughs', 1005: 'doug:', 1006: 'symphonies', 1007: 'rip-off', 1008: "nick's", 1009: 'strong', 1010: 'bottle', 1011: 'ancient', 1012: 'outlive', 1013: 'blow', 1014: 'slender', 1015: 'chic', 1016: 'renee:', 1017: 'months', 1018: 'marvin', 1019: 'muscle', 1020: 'shout', 1021: 'bounced', 1022: 'winks', 1023: 'upn', 1024: 'taxes', 1025: 'buyer', 1026: 'fatty', 1027: "world's", 1028: 'bart', 1029: 'de', 1030: 'exultant', 1031: 'poisoning', 1032: 'drove', 1033: 'swill', 1034: 'kisses', 1035: 'problems', 1036: "s'cuse", 1037: 'game', 1038: 'number', 1039: 'butter', 1040: 'bowie', 1041: 'games', 1042: 'shoots', 1043: 'bob', 1044: 'managing', 1045: 'kl5-4796', 1046: 'water', 1047: 'company', 1048: 'weekend', 1049: "tramp's", 1050: 'alcohol', 1051: 'irrelevant', 1052: 'grandiose', 1053: 'yourselves', 1054: 'phase', 1055: 'chocolate', 1056: 'samples', 1057: 'searching', 1058: 'concerned', 1059: 'choked-up', 1060: 'yours', 1061: 'freaking', 1062: 'mmm-hmm', 1063: 'tapping', 1064: 'permanent', 1065: 'carl', 1066: 'drift', 1067: 'thing', 1068: 'project', 1069: 'japanese', 1070: 'fun', 1071: 'data', 1072: "plaster's", 1073: 'choice', 1074: 'sweeter', 1075: 'anyway', 1076: 'issues', 1077: 'hugh', 1078: 'realize', 1079: 'looooooooooooooooooong', 1080: 'rebuttal', 1081: 'macgregor', 1082: 'thousands', 1083: 'disco_stu:', 1084: 'willy', 1085: 'compared', 1086: 'glasses', 1087: 'convinced', 1088: 'frontrunner', 1089: 'angel', 1090: 'cleaner', 1091: 'stairs', 1092: 'knife', 1093: 'banks', 1094: 'chip', 1095: 'nibble', 1096: 'hemorrhage-amundo', 1097: 'body', 1098: "o'clock", 1099: 'the_edge:', 1100: 'computer_voice_2:', 1101: 'wanted', 1102: 'measure', 1103: 'dae', 1104: 'feedbag', 1105: 'sieben-gruben', 1106: 'boxcar', 1107: "could've", 1108: 'kahlua', 1109: 'seven', 1110: 'telephone', 1111: 'appalled', 1112: 'violin', 1113: 'loboto-moth', 1114: 'gulliver_dark:', 1115: 'chapstick', 1116: 'tempting', 1117: "duelin'", 1118: 'smile:', 1119: 'winded', 1120: 'grabs', 1121: 'cuddling', 1122: 'nitwit', 1123: 'army', 1124: 'sneak', 1125: 'tones', 1126: 'hits', 1127: 'business', 1128: 'shindig', 1129: 'dime', 1130: 'bash', 1131: 'consider', 1132: 'talkers', 1133: 'off', 1134: 'presentable', 1135: 'fifteen', 1136: 'thawing', 1137: 'abolish', 1138: 'ralphie', 1139: "fightin'", 1140: 'fast-food', 1141: 'dan', 1142: 'eyeballs', 1143: 'legal', 1144: "carl's", 1145: 'planet', 1146: 'winces', 1147: 'fumes', 1148: 'patient', 1149: 'sport', 1150: 'wheeeee', 1151: 'cup', 1152: 'reynolds', 1153: 'puke-pail', 1154: 'cock', 1155: 'little', 1156: 'consciousness', 1157: 'spilled', 1158: 'alibi', 1159: 'transylvania', 1160: 'syrup', 1161: 'ironed', 1162: 'most:', 1163: 'mexican_duffman:', 1164: 'popping', 1165: 'confidentially', 1166: 'habit', 1167: "somethin':", 1168: 'signal', 1169: 'week', 1170: 'steal', 1171: "'morning", 1172: 'thought_bubble_lenny:', 1173: 'malabar', 1174: 'smitty:', 1175: 'uglier', 1176: 'edison', 1177: 'anti-crime', 1178: 'laughing', 1179: 'powered', 1180: 'conditioning', 1181: 'libido', 1182: 'bourbon', 1183: 'nevada', 1184: 'quitcher', 1185: 'eve', 1186: 'swooning', 1187: 'dizzy', 1188: 'third', 1189: 'bupkus', 1190: 'ford', 1191: 'spit', 1192: 'strain', 1193: 'modest', 1194: 'faceful', 1195: 'renee', 1196: 'worked', 1197: 'pajamas', 1198: 'overflowing', 1199: "lady's", 1200: 'numbers', 1201: 'suru', 1202: 'alone', 1203: 'madonna', 1204: 'spite', 1205: 'duffman', 1206: "buffalo's", 1207: 'poison', 1208: 'brain', 1209: 'souvenir', 1210: 'sun', 1211: "mtv's", 1212: 'video', 1213: 'manage', 1214: '_zander:', 1215: 'such', 1216: 'throws', 1217: 'apart', 1218: "larry's", 1219: 'model', 1220: 'duty', 1221: 'groin', 1222: 'sour', 1223: 'chief', 1224: 'cricket', 1225: 'threw', 1226: 'something', 1227: "fun's", 1228: 'ambrose', 1229: 'j', 1230: 'half-beer', 1231: 'darkest', 1232: 'courteous', 1233: 'paris', 1234: 'talk-sings', 1235: 'now', 1236: 'bon', 1237: 'recorded', 1238: 'introduce', 1239: 'cletus_spuckler:', 1240: "friend's", 1241: 'kako:', 1242: 'conclude', 1243: 'wishes', 1244: 'we-we-we', 1245: 'relaxed', 1246: 'sinkhole', 1247: 'represents', 1248: "bart's", 1249: 'later', 1250: 'wishing', 1251: 'doing', 1252: 'twenty', 1253: 'attached', 1254: 'surgeonnn', 1255: 'every', 1256: 'criminal', 1257: 'propose', 1258: 'gumbo', 1259: 'carny:', 1260: 'offer', 1261: 'paper', 1262: 'respect', 1263: 'admit', 1264: "poisonin'", 1265: 'ancestors', 1266: 'trade', 1267: "callin'", 1268: 'manuel', 1269: 'beloved', 1270: 'panties', 1271: 'stupidly', 1272: 'grease', 1273: 'check', 1274: 'feed', 1275: 'absentmindedly', 1276: 'managed', 1277: 'smiles', 1278: 'reunion', 1279: 'marry', 1280: 'flanders', 1281: 'intrigued', 1282: 'multi-national', 1283: 'eggshell', 1284: 'drop', 1285: 'charm', 1286: 'seat', 1287: "ol'", 1288: 'liability', 1289: 'straight', 1290: 'rationalizing', 1291: 'quarter', 1292: 'paparazzo', 1293: 'sales', 1294: 'richard', 1295: 'shaking', 1296: 'gayer', 1297: 'spend', 1298: 'jerk-ass', 1299: 'it:', 1300: 'desperate', 1301: 'any', 1302: 'mouse', 1303: 'cutting', 1304: 'complaining', 1305: 'k', 1306: 'pills', 1307: 'grind', 1308: 'root', 1309: 'cops', 1310: 'throwing', 1311: 'psst', 1312: 'starla:', 1313: 'mirror', 1314: 'line', 1315: 'slipped', 1316: 'bell', 1317: 'owes', 1318: 'meaningful', 1319: 'hoax', 1320: 'freshened', 1321: "she'd", 1322: 'voice', 1323: 'phlegm', 1324: 'die-hard', 1325: 'rekindle', 1326: 'cans', 1327: 'updated', 1328: 'peeping', 1329: 'trashed', 1330: "handwriting's", 1331: 'exhaust', 1332: 'radiation', 1333: 'oh', 1334: "drexel's", 1335: 'toys', 1336: 'dan_gillick:', 1337: 'gift:', 1338: 'walther', 1339: 'lady-free', 1340: 'reach', 1341: 'friends', 1342: 'leg', 1343: 'mock-up', 1344: 'mozzarella', 1345: 'son-of-a', 1346: 'believer', 1347: 'sucked', 1348: 'bottoms', 1349: 'shakes', 1350: 'pleasant', 1351: 'grammar', 1352: 'memory', 1353: 'say', 1354: "'roids", 1355: 'then', 1356: 'stay-puft', 1357: 'engraved', 1358: 'morose', 1359: 'krusty', 1360: 'delicious', 1361: 'listen', 1362: 'football', 1363: 'quimby_#2:', 1364: 'radishes', 1365: 'anti-lock', 1366: 'stillwater:', 1367: 'keys', 1368: 'how', 1369: 'wait', 1370: 'virile', 1371: 'harvey', 1372: 'espn', 1373: 'blew', 1374: 'manager', 1375: 'lazy', 1376: 'f', 1377: 'where', 1378: 'full-bodied', 1379: 'donut-shaped', 1380: 'faiths', 1381: 'lottery', 1382: 'rhyme', 1383: 'celebration', 1384: 'links', 1385: 'l', 1386: "rasputin's", 1387: 'margarita', 1388: 'yelp', 1389: 'troy', 1390: 'kindly', 1391: 'told', 1392: 'up-bup-bup', 1393: 'nailed', 1394: 'according', 1395: 'thousand-year', 1396: 'damned', 1397: 'mr', 1398: 'temp', 1399: 'willing', 1400: 'catty', 1401: "shan't", 1402: 'sports_announcer:', 1403: 'traitor', 1404: 'shells', 1405: 'brow', 1406: 'beer-jerks', 1407: 'midge:', 1408: 'sister-in-law', 1409: 'simultaneous', 1410: 'coined', 1411: "dimwit's", 1412: 'pure', 1413: "lisa's", 1414: 'sad', 1415: 'twenty-five', 1416: 'pinchpenny', 1417: 'wigs', 1418: 'churchy', 1419: "kids'", 1420: 'ga', 1421: 'hawking:', 1422: 'gotta', 1423: 'gonna', 1424: 'pas', 1425: 'womb', 1426: 'philip', 1427: '||question_mark||', 1428: "aristotle's", 1429: 'first', 1430: 'shoulda', 1431: 'egg', 1432: 'american', 1433: 'scram', 1434: 'abcs', 1435: 'roller', 1436: 'firm', 1437: 'thirty-five', 1438: 'short_man:', 1439: 'considering:', 1440: 'isotopes', 1441: 'mcstagger', 1442: 'small_boy:', 1443: "havin'", 1444: 'light', 1445: 'vampire', 1446: 'chumbawamba', 1447: 'cronies', 1448: "'n'", 1449: 'testing', 1450: 'holiday', 1451: 'figured', 1452: 'doof', 1453: 'fleabag', 1454: "pullin'", 1455: 'squashing', 1456: 'housewife', 1457: 'cologne', 1458: 'an', 1459: 'tony', 1460: 'proper', 1461: 'marched', 1462: 'feisty', 1463: 'clincher', 1464: 'threatening', 1465: '2', 1466: 'nominated', 1467: 'stories', 1468: 'sue', 1469: "father's", 1470: 'mitts', 1471: 'shocked', 1472: 'law-abiding', 1473: 'eventually', 1474: 'newly-published', 1475: 'showered', 1476: 'limericks', 1477: 'whistles', 1478: 'disapproving', 1479: 'marshmallow', 1480: 'sale', 1481: 'fluoroscope', 1482: 'selling', 1483: 'non-losers', 1484: 'hopeful', 1485: 'developed', 1486: 'pull', 1487: 'lover', 1488: 'rotten', 1489: 'sink', 1490: 'krusty_the_clown:', 1491: 'spoken', 1492: 'diapers', 1493: 'accelerating', 1494: 'aquafresh', 1495: 'you-need-man', 1496: "mcstagger's", 1497: 'halfway', 1498: 'heck', 1499: 'pipe', 1500: 'whining', 1501: '1895', 1502: 'naval', 1503: 'sickly', 1504: 'mona_simpson:', 1505: 'lately', 1506: 'burnside', 1507: 'belts', 1508: 'dictator', 1509: "somethin'", 1510: 'progress', 1511: 'accidents', 1512: 'cruiser', 1513: 'bathing', 1514: 'rom', 1515: 'buds', 1516: 'fat_tony:', 1517: 'amnesia', 1518: 'banned', 1519: 'nice', 1520: 'fix', 1521: 'star', 1522: 'kirk', 1523: 'teddy', 1524: 'queen', 1525: "fallin'", 1526: 'corkscrews', 1527: 'running', 1528: 'whoa-ho', 1529: 'marriage', 1530: 'discriminate', 1531: 'program', 1532: 'statesmanlike', 1533: 'attracted', 1534: 'sick', 1535: 'so-ng', 1536: 'married', 1537: 'sass', 1538: 'compliments', 1539: 'rush', 1540: 'tank', 1541: 'turns', 1542: 'other_player:', 1543: 'mop', 1544: "brockman's", 1545: 'chained', 1546: 'tourist', 1547: 'assume', 1548: 'literary', 1549: 'weapon', 1550: 'know', 1551: 'chateau', 1552: 'colossal', 1553: 'screws', 1554: 'vengeance', 1555: 'gator', 1556: 'pats', 1557: 'fortress', 1558: 'pickles', 1559: 'creme', 1560: 'dennis_conroy:', 1561: 'mamma', 1562: 'bald', 1563: 'tactful', 1564: 'sobriety', 1565: "'cause", 1566: 'technical', 1567: 'customers-slash-only', 1568: 'scientific', 1569: 'fit', 1570: 'begins', 1571: 'crowded', 1572: 'tester', 1573: 'dealie', 1574: 'mis-statement', 1575: 'hispanic_crowd:', 1576: 'ass', 1577: 'watashi', 1578: 'limited', 1579: 'suds', 1580: 'jackson', 1581: 'think', 1582: 'talked', 1583: 'dropping', 1584: 'happiness', 1585: 'sleep', 1586: 'old', 1587: 'thirsty', 1588: 'hiring', 1589: 'ihop', 1590: 'hemoglobin', 1591: 'examines', 1592: 'kept', 1593: 'frosty', 1594: 'boy', 1595: 'safecracker', 1596: 'neighboreeno', 1597: 'railroads', 1598: 'intimacy', 1599: 'shoe', 1600: 'conspiracy', 1601: 'curds', 1602: 'luv', 1603: 'weather', 1604: 'adopted', 1605: 'chauffeur', 1606: 'corn', 1607: 'civic', 1608: 'ashamed', 1609: 'executive', 1610: 'dumpster', 1611: 'hands', 1612: 'square', 1613: '||left_paren||', 1614: 'snow', 1615: 'teenage_bart:', 1616: 'eu', 1617: 'today/', 1618: 'liar', 1619: 'veteran', 1620: 'spent', 1621: 'ralph', 1622: 'ummmmmmmmm', 1623: 'pin', 1624: 'locked', 1625: 'loneliness', 1626: 'platinum', 1627: 'skills', 1628: 'center', 1629: 'mouths', 1630: 'jebediah', 1631: 'cab', 1632: 'should', 1633: "robbin'", 1634: 'valuable', 1635: 'hydrant', 1636: 'order', 1637: 'ice', 1638: 'myself', 1639: 'could', 1640: 'mother', 1641: 'offensive', 1642: 'intelligent', 1643: 'quality', 1644: 'ahhh', 1645: 'calvin', 1646: 'british', 1647: 'life-threatening', 1648: 'wowww', 1649: 'own', 1650: 'fall', 1651: 'starlets', 1652: 'size', 1653: "spaghetti-o's", 1654: 'artie_ziff:', 1655: 'index', 1656: 'calmly', 1657: 'hurting', 1658: 'deep', 1659: 'koi', 1660: "sat's", 1661: 'warily', 1662: 'carll', 1663: 'expired', 1664: 'getting', 1665: 'prayer', 1666: 'rash', 1667: 'formico:', 1668: 'eleven', 1669: 'half', 1670: 'wistful', 1671: 'stolen', 1672: 'panicked', 1673: "plank's", 1674: 'shack', 1675: 'homer', 1676: 'or', 1677: 'magnanimous', 1678: 'afterglow', 1679: 'hoo', 1680: 'riding', 1681: 'finding', 1682: 'lucius:', 1683: 'early', 1684: 'parking', 1685: "countin'", 1686: 'griffith', 1687: 'sponge:', 1688: 'love-matic', 1689: 'rusty', 1690: 'manipulation', 1691: 'conference', 1692: "coffee'll", 1693: 'improved', 1694: 'shoulders', 1695: 'awesome', 1696: 'sing-song', 1697: 'hanh', 1698: 'burglary', 1699: 'held', 1700: 'simpson', 1701: 'patron_#1:', 1702: 'attitude', 1703: 'hippies', 1704: 'weep', 1705: 'furious', 1706: 'theatah', 1707: 'scoffs', 1708: 'sacrifice', 1709: 'show', 1710: 'picnic', 1711: 'bumbling', 1712: 'wakede', 1713: 'frozen', 1714: 'skin', 1715: 'afraid', 1716: 'sheriff', 1717: "president's", 1718: 'bachelor', 1719: 'scent', 1720: "ridin'", 1721: 'roz:', 1722: 'higher', 1723: 'widow', 1724: 'group', 1725: 'mid-conversation', 1726: 'victory', 1727: 'through', 1728: 'omit', 1729: 'elder', 1730: "how're", 1731: 'finance', 1732: 'can', 1733: 'picked', 1734: 'hottest', 1735: 'tobacky', 1736: 'drunks', 1737: 'seem', 1738: 'raising', 1739: 'vigilante', 1740: 'puzzle', 1741: 'ladies', 1742: 'lloyd:', 1743: 'joy', 1744: 'snackie', 1745: 'cheerleaders:', 1746: "where's", 1747: 'boys', 1748: "g'on", 1749: 'stationery', 1750: 'gosh', 1751: 'upsetting', 1752: 'raining', 1753: 'since', 1754: 'thrust', 1755: 'disappeared', 1756: 'soaking', 1757: 'ironic', 1758: 'opportunity', 1759: 'grow', 1760: 'mccarthy', 1761: 'crank', 1762: 'academy', 1763: 'answer', 1764: 'card', 1765: 'way', 1766: 'anniversary', 1767: 'art', 1768: 'ayyy', 1769: 'black', 1770: "football's", 1771: 'host', 1772: 'craphole', 1773: 'washouts', 1774: 'what-for', 1775: 'flower', 1776: 'creates', 1777: 'rap', 1778: 'prints', 1779: 'sector', 1780: "edna's", 1781: 'alter', 1782: 'kids', 1783: 'faulkner', 1784: 'hitler', 1785: 'sleeping', 1786: 'gums', 1787: 'ambrosia', 1788: 'easygoing', 1789: 'anguished', 1790: 'enforced', 1791: 'illustrates', 1792: 'temper', 1793: 'medicine', 1794: 'gel', 1795: "bladder's", 1796: 'goes', 1797: 'plow', 1798: 'public', 1799: 'famous', 1800: 'coming', 1801: 'drawer', 1802: 'subscriptions', 1803: 'floated', 1804: 'dna', 1805: 'front', 1806: 'jer', 1807: 'bookie', 1808: 'rough', 1809: 'reaching', 1810: 'woozy', 1811: 'west', 1812: 'movement', 1813: 'bliss', 1814: 'civil', 1815: 'investigating', 1816: 'interrupting', 1817: "bo's", 1818: 'sanitation', 1819: 'other_book_club_member:', 1820: 'unjustly', 1821: 'applicant', 1822: 'cliff', 1823: 'paying', 1824: 'yo', 1825: 'day', 1826: 'twerpy', 1827: 'occupation', 1828: 'distinct', 1829: 'boxing_announcer:', 1830: 'realizing', 1831: 'relaxing', 1832: 'iran', 1833: 'justify', 1834: 'cheaped', 1835: "stabbin'", 1836: 'title:', 1837: 'involved', 1838: 'relieved', 1839: 'resenting', 1840: 'tabooger', 1841: 'unavailable', 1842: 'gluten', 1843: 'kent_brockman:', 1844: 'country-fried', 1845: "others'", 1846: 'deals', 1847: 'reckless', 1848: "what'll", 1849: 'hmm', 1850: 'seemed', 1851: 'shop', 1852: 'archaeologist', 1853: 'title', 1854: 'tip', 1855: 'resist', 1856: 'shoulder', 1857: 'butt', 1858: 'very', 1859: 'zeal', 1860: 'language', 1861: 'dig', 1862: "smackin'", 1863: 'regretful', 1864: 'thnord', 1865: 'interesting', 1866: 'plums', 1867: 'eyes', 1868: '91', 1869: 'legally', 1870: 'i-i', 1871: 'stomach', 1872: "'s", 1873: 'stupidest', 1874: 'stink', 1875: 'singing/pushing', 1876: 'lowers', 1877: 'gary_chalmers:', 1878: 'tinkle', 1879: 'mom', 1880: 'end', 1881: 'watt', 1882: 'stripes', 1883: 'ridiculous', 1884: 'gotten', 1885: 'suck', 1886: 'blend', 1887: 'monkeyshines', 1888: "wallet's", 1889: 'agnes_skinner:', 1890: 'device', 1891: 'western', 1892: 'angry', 1893: 'really', 1894: 'on', 1895: 'thirty-nine', 1896: 'stools', 1897: 'manfred', 1898: 'a-a-b-b-a', 1899: 'health', 1900: 'military', 1901: 'wow', 1902: 'hot', 1903: 'break-up', 1904: 'restroom', 1905: 'shareholder', 1906: 'tribute', 1907: 'duff', 1908: 'wishful', 1909: 'six-barrel', 1910: 'taylor', 1911: 'set', 1912: 'si-lent', 1913: 'proposing', 1914: 'offense', 1915: "people's", 1916: 'heard', 1917: 'entirely', 1918: 'run', 1919: 'hmmm', 1920: 'sternly', 1921: 'dungeon', 1922: 'help', 1923: 'pardon', 1924: 'cowboys', 1925: 'indignant', 1926: 'plywood', 1927: 'recorder', 1928: 'edge', 1929: 'present', 1930: 'na', 1931: 'endorse', 1932: 'pre-game', 1933: 'harder', 1934: 'changing', 1935: 'singing', 1936: 'society_matron:', 1937: 'reactions', 1938: 'puts', 1939: 'sneeze', 1940: 'notorious', 1941: 'inspection', 1942: 'meatpies', 1943: 'push', 1944: 'train', 1945: 'let', 1946: 'pussycat', 1947: 'quit', 1948: 'moe-lennium', 1949: "meanin'", 1950: 'authenticity', 1951: 'travel', 1952: 'sticking-place', 1953: 'taxi', 1954: 'painted', 1955: 'beast', 1956: 'outrageous', 1957: 'rolls', 1958: "doin'", 1959: 'walking', 1960: 'doooown', 1961: 'uhhhh', 1962: 'team', 1963: 'burn', 1964: 'harv', 1965: 'industry', 1966: 'chug-a-lug', 1967: 'snotty', 1968: 'never', 1969: 'dollface', 1970: 'local', 1971: 'gun', 1972: "maggie's", 1973: 'linda', 1974: 'veux', 1975: 'badges', 1976: 'recreate', 1977: 'michael_stipe:', 1978: 'just', 1979: 'store-bought', 1980: "wouldn't-a", 1981: 'motor', 1982: 'felony', 1983: 'bully', 1984: 'flayvin', 1985: "today's", 1986: 'burt', 1987: 'fox_mulder:', 1988: 'ads', 1989: 'salvation', 1990: 'damage', 1991: 'mailbox', 1992: 'startup', 1993: "monroe's", 1994: 'super', 1995: 'bubbles', 1996: 'aims', 1997: 'quiet', 1998: "everyone's", 1999: 'spending', 2000: 'brandy', 2001: 'our', 2002: 'bee', 2003: 'powers', 2004: 'booking', 2005: 'his', 2006: 'champignons', 2007: 'toasting', 2008: 'tanked-up', 2009: 'fbi', 2010: 'nation', 2011: 'eyesore', 2012: 'helllp', 2013: 'quebec', 2014: 'wall', 2015: 'poplar', 2016: 'options', 2017: 'pulitzer', 2018: 'ratio', 2019: 'alec_baldwin:', 2020: "feelin's", 2021: 'smokes', 2022: 'edna_krabappel-flanders:', 2023: 'risquã©', 2024: 'cigars', 2025: 'glowers', 2026: 'kool', 2027: 'feminine', 2028: 'stupid', 2029: "aren't", 2030: 'presto:', 2031: 'lucky', 2032: 'faith', 2033: 'theater', 2034: 'side', 2035: 'kitchen', 2036: 'wine', 2037: 'staying', 2038: 'suspenders', 2039: 'wolfe', 2040: 'polishing', 2041: 'tie', 2042: 'virtual', 2043: 'coal', 2044: 'works', 2045: 'hot-rod', 2046: 'dentist', 2047: 'knew', 2048: 'haiti', 2049: 'application', 2050: 'forty-five', 2051: 'stadium', 2052: 'truck', 2053: 'label', 2054: 'perhaps', 2055: 'bible', 2056: 'simp-sonnnn', 2057: 'confidence', 2058: 'remaining', 2059: 'man_with_tree_hat:', 2060: 'horses', 2061: 'initially', 2062: 'shutup', 2063: 'whip', 2064: 'scooter', 2065: 'guide', 2066: 'hello', 2067: 'dyspeptic', 2068: 'nash', 2069: 'glummy', 2070: 'wiggle', 2071: 'baritone', 2072: 'delightful', 2073: "pope's", 2074: 'chicken', 2075: 'people', 2076: 'selma_bouvier:', 2077: 'minus', 2078: 'highway', 2079: "listenin'", 2080: 'nelson', 2081: 'supports', 2082: 'dumb-asses', 2083: 'crony', 2084: 'creature', 2085: 'pharmaceutical', 2086: 'luxury', 2087: 'sacajawea', 2088: 'kegs', 2089: 'whoops', 2090: 'vanities', 2091: 'luckily', 2092: "lovers'", 2093: 'runaway', 2094: 'giggle', 2095: 'yet', 2096: 'attractive_woman_#1:', 2097: 'notice', 2098: 'bad-mouth', 2099: 'agreement', 2100: 'regulations', 2101: 'leave', 2102: 'stuck', 2103: 'thousand', 2104: 'amazed', 2105: 'multiple', 2106: 'figures', 2107: 'outta', 2108: 'indeedy', 2109: 'suits', 2110: 'shares', 2111: 'bulked', 2112: "calf's", 2113: 'hilarious', 2114: 'treats', 2115: 'anti-intellectualism', 2116: 'type', 2117: 'bumblebee_man:', 2118: 'wrestling', 2119: 'bachelorette', 2120: 'child', 2121: 'twelve-step', 2122: 'truth', 2123: 'replaced', 2124: 'windelle', 2125: 'today', 2126: 'baseball', 2127: 'challenge', 2128: 'selection', 2129: 'cooker', 2130: 'ferry', 2131: 'swatch', 2132: 'chug-monkeys', 2133: 'bill_james:', 2134: 'dutch', 2135: 'charter', 2136: 'lovely', 2137: 'whim', 2138: 'cockroaches', 2139: 'writing', 2140: 'onion', 2141: 'uncle', 2142: 'xanders', 2143: 'butterball', 2144: 'photos', 2145: 'mexican', 2146: 'extended', 2147: 'sugar', 2148: 'hub', 2149: '14', 2150: 'cold', 2151: 'mabel', 2152: 'crap', 2153: 'sloe', 2154: 'column', 2155: 'collapse', 2156: 'charge', 2157: 'murmurs', 2158: 'death', 2159: 'bites', 2160: 'sunday', 2161: 'temporarily', 2162: 'snort', 2163: 'insurance', 2164: 'jackpot-thief', 2165: 'bono', 2166: 'highest', 2167: 'latin', 2168: 'inanely', 2169: 'occupied', 2170: 'paid', 2171: 'quarterback', 2172: 'backgammon', 2173: 'bulletin', 2174: "don't", 2175: 'sidelines', 2176: "that'd", 2177: "changin'", 2178: 'dang', 2179: "high-falutin'", 2180: 'fontaine', 2181: 'gunter', 2182: 'indecipherable', 2183: 'businessman_#2:', 2184: 'landfill', 2185: 'carney', 2186: 'frankenstein', 2187: 'elizabeth', 2188: 'playing', 2189: 'barkeep', 2190: 'candy', 2191: 'thanking', 2192: 'spit-backs', 2193: 'ech', 2194: 'four-star', 2195: "man's", 2196: 'sniffles', 2197: 'mull', 2198: 'love', 2199: 'sorry', 2200: 'eat', 2201: 'jailbird', 2202: 'matter-of-fact', 2203: 'rule', 2204: 'discussing', 2205: 'recent', 2206: 'expression', 2207: 'typed', 2208: 'we', 2209: 'solves', 2210: 'fat_in_the_hat:', 2211: "seein'", 2212: 'alfalfa', 2213: "i've", 2214: 'disaster', 2215: 'certificate', 2216: 'meet', 2217: 'roses', 2218: 'surprising', 2219: 'fantastic', 2220: 'science', 2221: 'atlanta', 2222: 'quite', 2223: 'sealed', 2224: 'call', 2225: 'ma', 2226: 'lap', 2227: 'macbeth', 2228: 'mary', 2229: 'fools', 2230: 'bears', 2231: 'grabbing', 2232: 'lee', 2233: 'voyager', 2234: 'shotgun', 2235: 'praise', 2236: 'house', 2237: 'childless', 2238: 'tow', 2239: 'oil', 2240: 'plain', 2241: "they'll", 2242: 'snitch', 2243: 'simplest', 2244: 'specialists', 2245: 'adrift', 2246: 'car', 2247: 'collette:', 2248: "hobo's", 2249: 'cousin', 2250: 'sympathy', 2251: 'detective_homer_simpson:', 2252: 'ourselves', 2253: 'reading', 2254: 'ninth', 2255: 'cakes', 2256: 'advance', 2257: 'case', 2258: 'horrible', 2259: 'mock', 2260: "s'okay", 2261: "soakin's", 2262: 'smell', 2263: 'television', 2264: 'wolverines', 2265: 'betrayed', 2266: 'average-looking', 2267: 'president', 2268: "enjoyin'", 2269: 'bathroom', 2270: 'radiator', 2271: 'perfunctory', 2272: 'splash', 2273: 'recap:', 2274: 'contractors', 2275: "g'night", 2276: 'nasty', 2277: 'stooges', 2278: 'salary', 2279: 'holds', 2280: 'brag', 2281: 'waters', 2282: 'jumps', 2283: 'sports', 2284: 'knowledge', 2285: 'hear', 2286: 'adult', 2287: "battin'", 2288: 'poetry', 2289: 'nobel', 2290: 'hour', 2291: 'glen:', 2292: 'charlie:', 2293: 'prejudice', 2294: 'refreshment', 2295: 'position', 2296: 'delightfully', 2297: 'ripper', 2298: 'extinguishers', 2299: 'supply', 2300: 'pepsi', 2301: 'aid', 2302: 'cheered', 2303: 'detecting', 2304: 'party', 2305: 'dirge-like', 2306: 'insecure', 2307: 'nicer', 2308: 'return', 2309: 'pretentious_rat_lover:', 2310: 'focused', 2311: 'swig', 2312: 'beer', 2313: 'scornfully', 2314: 'woo', 2315: 'blocked', 2316: 'eighty-five', 2317: 'drunk', 2318: 'spirit', 2319: 'customer', 2320: "somebody's", 2321: 'federal', 2322: 'stern', 2323: "tootin'", 2324: 'fictional', 2325: 'bags', 2326: 'mmmmm', 2327: 'salvador', 2328: 'gig', 2329: 'calm', 2330: 'nos', 2331: "workin'", 2332: 'answered', 2333: 'lying', 2334: 'th-th-th-the', 2335: 'yello', 2336: 'floating', 2337: 'nope', 2338: 'book', 2339: 'harrowing', 2340: 'change', 2341: 'lots', 2342: 'there', 2343: 'priest', 2344: 'liser', 2345: 'promotion', 2346: 'bushes', 2347: 'twentieth', 2348: "here's", 2349: 'authorized', 2350: 'subject', 2351: 'was', 2352: 'mediterranean', 2353: 'concentrate', 2354: 'wantcha', 2355: 'aerospace', 2356: "choosin'", 2357: 'fledgling', 2358: 'nagurski', 2359: 'lovelorn', 2360: "he'll", 2361: 'jeter', 2362: 'foodie', 2363: 'nobody', 2364: 'scum', 2365: 'horror', 2366: 'y-you', 2367: "i'd", 2368: 'schabadoo', 2369: 'foibles', 2370: "he's", 2371: 'daniel', 2372: 'dump', 2373: 'moe', 2374: 'post-suicide', 2375: 'caricature', 2376: 'borrow', 2377: 'billboard', 2378: 'road', 2379: 'colonel:', 2380: "can'tcha", 2381: 'three', 2382: 'directions', 2383: 'general', 2384: 'briefly', 2385: 'smelling', 2386: 'yee-ha', 2387: 'goblins', 2388: 'yoink', 2389: "wait'll", 2390: "'ere", 2391: 'bothered', 2392: 'jukebox_record:', 2393: 'moving', 2394: 'hollowed-out', 2395: 'gets', 2396: 'row', 2397: '_marvin_monroe:', 2398: "i-i'll", 2399: 'orphan', 2400: 'gloop', 2401: 'keep', 2402: 'benefits', 2403: 'five-fifteen', 2404: 'toe', 2405: 'x-men', 2406: 'baloney', 2407: 'fruit', 2408: 'tastes', 2409: 'peace', 2410: 'bouquet', 2411: 'extreme', 2412: '_kissingher:', 2413: 'garbage', 2414: 'circus', 2415: 'nachos', 2416: 'rife', 2417: 'pretend', 2418: "tree's", 2419: 'unattended', 2420: 'bucks', 2421: 'wuss', 2422: 'vestigial', 2423: "startin'", 2424: 't-shirt', 2425: 'snaps', 2426: 'funds', 2427: 'whaaa', 2428: 'anthony_kiedis:', 2429: 'panicky', 2430: 'isle', 2431: 'wobbly', 2432: 'fool', 2433: 'been', 2434: 'clown-like', 2435: 'drinking', 2436: "jackpot's", 2437: 'killed', 2438: 'plants', 2439: 'browns', 2440: 'grammy', 2441: 'insults', 2442: 'fail', 2443: 'sigh', 2444: 'spy', 2445: 'bar_rag:', 2446: 'friend:', 2447: 'nature', 2448: 'occasion', 2449: 'test', 2450: 'vulgar', 2451: '6', 2452: 'worthless', 2453: 'ripped', 2454: 'sticker', 2455: 'lindsay', 2456: 'student', 2457: 'everything', 2458: 'kyoto', 2459: 'ali', 2460: 'lady', 2461: 'bit', 2462: 'slice', 2463: 'flames', 2464: 'bumped', 2465: 'vodka', 2466: 'wins', 2467: 'zero', 2468: "games'd", 2469: 'pointed', 2470: 'clapping', 2471: 'unearth', 2472: 'dumptruck', 2473: 'elmer', 2474: 'tells', 2475: 'bits', 2476: 'pine', 2477: 'triangle', 2478: 'wife-swapping', 2479: 'he', 2480: 'either', 2481: 'stained-glass', 2482: 'apply', 2483: 'meyerhof', 2484: 'icy', 2485: 'catching', 2486: 'abercrombie', 2487: 'owned', 2488: 'out', 2489: 'choice:', 2490: 'fair', 2491: 'mission', 2492: 'ratted', 2493: 'larson', 2494: 'want', 2495: 'compete', 2496: 'payday', 2497: 'senator', 2498: 'hibachi', 2499: 'planned', 2500: 'season', 2501: 'shove', 2502: 'brains', 2503: 'hit', 2504: 'nerve', 2505: 'paints', 2506: 'hole', 2507: 'fiction', 2508: 'disdainful', 2509: 'mall', 2510: 'hitchhike', 2511: 'generous', 2512: "d'ya", 2513: 'las', 2514: 'kicked', 2515: 'aggravated', 2516: 'hearse', 2517: 'patron_#2:', 2518: "breakin'", 2519: 'b-day', 2520: 'tale', 2521: 'muscles', 2522: 'voice:', 2523: 'pained', 2524: 'gal', 2525: 'merchants', 2526: 'difference', 2527: 'scary', 2528: 'malibu', 2529: 'w', 2530: 'effigy', 2531: 'wonder', 2532: 'worse', 2533: 'monkey', 2534: 'diets', 2535: "patrick's", 2536: 'cooler', 2537: 'built', 2538: 'aggie', 2539: 'most', 2540: 'flush-town', 2541: 'rabbits', 2542: "moe's", 2543: 'outs', 2544: 'environment', 2545: 'joe', 2546: 'mean', 2547: 'effervescent', 2548: 'pretzel', 2549: "tony's", 2550: 'crab', 2551: 'generously', 2552: 'new_health_inspector:', 2553: 'thirty-thousand', 2554: 'brainiac', 2555: 'other', 2556: 'absolut', 2557: 'bullet-proof', 2558: 'provide', 2559: 'maman', 2560: 'outlook', 2561: 'peter', 2562: 'white', 2563: 'showing', 2564: 'enlightened', 2565: 'forgotten', 2566: 'gallon', 2567: 'oughta', 2568: 'slightly', 2569: 'lay', 2570: 'undated', 2571: 'wiener', 2572: 'utility', 2573: 'lou:', 2574: 'guess', 2575: 'beligerent', 2576: 'tree', 2577: 'crowds', 2578: 'celebrity', 2579: 'swell', 2580: 'slab', 2581: 'hiding', 2582: "readin'", 2583: 'rope', 2584: 'evil', 2585: 'dogs', 2586: 'bum:', 2587: 'inside', 2588: 'physical', 2589: 'absorbent', 2590: 'enthused', 2591: "you'd", 2592: 'reconsidering', 2593: 'official', 2594: 'wings', 2595: 'pigs', 2596: 'warn', 2597: "o'reilly", 2598: 'fustigation', 2599: 'invited', 2600: 'astronaut', 2601: 'yards', 2602: 'above', 2603: 'flexible', 2604: 'divine', 2605: 'ungrateful', 2606: "car's", 2607: 'heavyweight', 2608: 'degradation', 2609: 'cranberry', 2610: 'unexplained', 2611: 'sincere', 2612: "talkin'", 2613: 'jumping', 2614: 'bless', 2615: "idea's", 2616: 'come', 2617: 'acquitted', 2618: 'jovial', 2619: 'trucks', 2620: 'cobra', 2621: 'suit', 2622: 'kick-ass', 2623: 'buy', 2624: 'drive', 2625: 'backwards', 2626: 'towed', 2627: 'sleigh-horses', 2628: 'tough', 2629: 'point', 2630: 'recently', 2631: 'common', 2632: 'write', 2633: 'especially', 2634: 'specializes', 2635: 'name:', 2636: 'portfolium', 2637: 'produce', 2638: 'dimly', 2639: 'fuhgetaboutit', 2640: 'hired', 2641: 'proud', 2642: "fine-lookin'", 2643: 'laugh', 2644: 'africa', 2645: 'punkin', 2646: 'supposed', 2647: 'ollie', 2648: 'sob', 2649: 'word', 2650: 'modern', 2651: 'court', 2652: 'alright', 2653: 'brassiest', 2654: 'action', 2655: 're:', 2656: 'rounds', 2657: 'plum', 2658: 'replace', 2659: 'sobbing', 2660: 'troubles', 2661: 'took', 2662: 'ned_flanders:', 2663: 'guns', 2664: "d'", 2665: 'obvious', 2666: 'miles', 2667: 'wash', 2668: 'shakespeare', 2669: 'found', 2670: 'delts', 2671: 'energy', 2672: 'grave', 2673: 'attend', 2674: 'tried', 2675: 'losing', 2676: 'cream', 2677: "tonight's", 2678: 'spreads', 2679: 'asleep', 2680: "phone's", 2681: 'thought_bubble_homer:', 2682: 'declare', 2683: 'grudgingly', 2684: 'flown', 2685: 'alcoholism', 2686: 'bloodball', 2687: 'important', 2688: 'jets', 2689: 'sound', 2690: 'miracle', 2691: 'coat', 2692: 'invulnerable', 2693: 'toilet', 2694: "hole'", 2695: 'thrilled', 2696: 'indifferent', 2697: 'van', 2698: 'watch', 2699: 'lives', 2700: 'hand', 2701: 'unfortunately', 2702: 'kwik-e-mart', 2703: 'tommy', 2704: 'colorado', 2705: 'polls', 2706: 'fifty', 2707: 'trainers', 2708: 'smurfs', 2709: 'sausage', 2710: 'kenny', 2711: 'gheet', 2712: 'lookalike', 2713: 'coin', 2714: 'gabriel:', 2715: 'oils', 2716: 'watching', 2717: 'turkey', 2718: 'script', 2719: 'tatum', 2720: "weren't", 2721: 'cats', 2722: 'goldarnit', 2723: 'poin-dexterous', 2724: 'remains', 2725: 'bitter', 2726: 'bluff', 2727: 'amber', 2728: 'may', 2729: 'earrings', 2730: 'hardwood', 2731: 'done', 2732: 'mcbain', 2733: 'poet', 2734: 'pursue', 2735: 'julep', 2736: 'virility', 2737: 'papa', 2738: "what're", 2739: 'teacher', 2740: "man's_voice:", 2741: 'small', 2742: 'boxing', 2743: 'pretending', 2744: 'louie:', 2745: 'greatest', 2746: 'online', 2747: 'blowfish', 2748: 'none', 2749: 'gumbel', 2750: 'fonda', 2751: 'full', 2752: 'remain', 2753: 'agency', 2754: 'lifetime', 2755: 'socialize', 2756: 'thorough', 2757: 'rainbows', 2758: 'soup', 2759: 'bowling', 2760: 'indigenous', 2761: 'finish', 2762: 'klown', 2763: 'homunculus', 2764: 'suffering', 2765: '_eugene_blatz:', 2766: 'britannia', 2767: 'moe_recording:', 2768: 'matter', 2769: 'stored', 2770: 'beep', 2771: 'voters', 2772: 'gordon', 2773: 'flustered', 2774: '_julius_hibbert:', 2775: 'brave', 2776: 'please', 2777: 'named', 2778: 'racially-diverse', 2779: 'race', 2780: 'last', 2781: 'fellas', 2782: 'bon-bons', 2783: 'menacing', 2784: 'wake', 2785: 'bleacher', 2786: 'pennies', 2787: 'strains', 2788: 'dough', 2789: 'makes', 2790: 'nordiques', 2791: 'send', 2792: 'chanting', 2793: 'mags', 2794: 'donation', 2795: 'focus', 2796: 'professional', 2797: 'shriners', 2798: 'dressed', 2799: 'friendship', 2800: 'practice', 2801: 'advertising', 2802: 'decadent', 2803: 'weirder', 2804: 'mice', 2805: 'eternity', 2806: 'need', 2807: 'remembered', 2808: 'moans', 2809: 'duff_announcer:', 2810: 'combination', 2811: 'exchanged', 2812: 'kidding', 2813: 'forty-seven', 2814: 'passion', 2815: 'skeptical', 2816: "didn't", 2817: 'secrets', 2818: 'hops', 2819: 'seats', 2820: 'coyly', 2821: 'clothespins', 2822: 'suspended', 2823: 'flame', 2824: 'onto', 2825: 'error', 2826: 'refresh', 2827: 'walked', 2828: 'felt', 2829: 'breath', 2830: 'ten', 2831: 'congoleum', 2832: 'injury', 2833: 'shrugging', 2834: 'schedule', 2835: 'lenford', 2836: 'incarcerated', 2837: 'ballclub', 2838: '||dash||', 2839: 'humanity', 2840: 'ireland', 2841: 'repeated', 2842: 'spits', 2843: 'diablo', 2844: 'elaborate', 2845: 'sometime', 2846: 'soon', 2847: 'pirate', 2848: 'pernt', 2849: 'premise', 2850: 'right', 2851: 'vicious', 2852: 'uneasy', 2853: 'brainheaded', 2854: 'cracked', 2855: 'african', 2856: 'musketeers', 2857: 'ingested', 2858: 'dashes', 2859: "stinkin'", 2860: 'deeply', 2861: 'infatuation', 2862: 'starla', 2863: 'saga', 2864: 'waylon', 2865: 'act', 2866: 'ones', 2867: 'self', 2868: 'understood:', 2869: 'unfamiliar', 2870: 'prime', 2871: 'its', 2872: 'hammer', 2873: 'system', 2874: 'ventriloquism', 2875: 'knocked', 2876: 'prove', 2877: 'crawl', 2878: 'reader', 2879: 'dollar', 2880: 'boxers', 2881: 'villanova', 2882: 'waterfront', 2883: 'therapist', 2884: 'clammy', 2885: 'jerks', 2886: 'korea', 2887: 'your', 2888: 'recall', 2889: 'original', 2890: 'noises', 2891: 'crippling', 2892: 'wearing', 2893: 'beaumarchais', 2894: 'je', 2895: 'throat', 2896: 'jeff', 2897: 'bridges', 2898: 'aunt', 2899: 'don', 2900: 'nuts', 2901: "ma'am", 2902: 'cheer', 2903: "i'unno", 2904: 'sat-is-fac-tion', 2905: 'barbed', 2906: 'heave-ho', 2907: 'package', 2908: 'grumbling', 2909: "you'll", 2910: 'grace', 2911: 'phrase', 2912: 'sinister', 2913: 'yellow', 2914: 'someone', 2915: 'park', 2916: 'dropped', 2917: 'prices', 2918: 'cadillac', 2919: 'mahatma', 2920: "starla's", 2921: 'states', 2922: 'comforting', 2923: 'girlfriend', 2924: 'them', 2925: 'straining', 2926: 'to', 2927: 'texan', 2928: 'idealistic', 2929: 'timbuk-tee', 2930: 'grrrreetings', 2931: 'stumble', 2932: 'steely-eyed', 2933: 'binoculars', 2934: 'saucy', 2935: 'ooo', 2936: 'ideal', 2937: 'ken:', 2938: 'gone', 2939: 'switch', 2940: 'de-scramble', 2941: 'jail', 2942: 'corkscrew', 2943: 'tenuous', 2944: "'now", 2945: 'moe-heads', 2946: 'aziz', 2947: 'tomatoes', 2948: 'studio', 2949: "ragin'", 2950: 'jamaican', 2951: 'indicates', 2952: 'image', 2953: 'minute', 2954: 'pool', 2955: 'chase', 2956: 'happened', 2957: 'bedroom', 2958: 'hellhole', 2959: 'fuss', 2960: 'hated', 2961: 'atari', 2962: 'pen', 2963: 'music', 2964: 'stool', 2965: 'police', 2966: 'exclusive:', 2967: 'slays', 2968: 'corporate', 2969: 'street', 2970: 'menace', 2971: 'normals', 2972: "'til", 2973: 'enveloped', 2974: 'tropical', 2975: 'lighten', 2976: 'laws', 2977: 'simple', 2978: 'soul-crushing', 2979: 'scornful', 2980: 'miss', 2981: "waitin'", 2982: 'choices:', 2983: 'french', 2984: 'rockers', 2985: 'jubilation', 2986: 'gossipy', 2987: 'high', 2988: "y'know", 2989: 'monorails', 2990: 'shoo', 2991: 'story', 2992: "professor's", 2993: 'hard', 2994: 'beneath', 2995: 'uncomfortable', 2996: 'job', 2997: 'noosey', 2998: 'sumatran', 2999: 'living', 3000: 'welcome', 3001: 'united', 3002: 'sued', 3003: 'way:', 3004: 'piling', 3005: 'called', 3006: 'southern', 3007: 'count', 3008: 'wipes', 3009: 'poured', 3010: 'all-all-all', 3011: 'instead', 3012: 'choke', 3013: 'shower', 3014: 'gorgeous', 3015: 'con', 3016: 'throw', 3017: 'restaurant', 3018: 'incapable', 3019: 'precious', 3020: 'minutes', 3021: "fryer's", 3022: 'distraught', 3023: 'taking', 3024: 'mister', 3025: 'williams', 3026: 'carnival', 3027: 'fantasy', 3028: 'corner', 3029: 'during', 3030: 'purveyor', 3031: 'handle', 3032: 'arabs', 3033: 'list', 3034: 'troll', 3035: 'debonair', 3036: 'exits', 3037: 'slaps', 3038: 'sixteen', 3039: 'fridge', 3040: 'leaving', 3041: 'rumor', 3042: 'a-lug', 3043: 'sack', 3044: "they'd", 3045: 'gestated', 3046: 'greedy', 3047: 'onassis', 3048: 'cooking', 3049: 'all-star', 3050: 'handing', 3051: 'vance', 3052: 'springfield', 3053: 'handoff', 3054: 'safety', 3055: 'hurt', 3056: 'food', 3057: 'resolution', 3058: 'dignity', 3059: 'grinch', 3060: 'lance', 3061: 'free', 3062: 'came', 3063: 'legs:', 3064: 'repairman', 3065: 'man_with_crazy_beard:', 3066: 'jobs', 3067: 'creepy', 3068: 'youuu', 3069: 'sarcastic', 3070: 'simon', 3071: 'procedure', 3072: 'suspect', 3073: 'gargoyles', 3074: 'demand', 3075: 'go', 3076: 'pilsner-pusher', 3077: 'optimistic', 3078: 'lost', 3079: 'candles', 3080: 'chin', 3081: 'uh', 3082: 'asses', 3083: 'life-extension', 3084: 'completing', 3085: 'encouraged', 3086: 'fist', 3087: 'transfer', 3088: 'poetics', 3089: 'serum', 3090: 'nightmare', 3091: 'drivers', 3092: 'motel', 3093: 'pick', 3094: 'unsafe', 3095: 'sanctuary', 3096: 'gut', 3097: 'satisfaction', 3098: 'asked', 3099: 'so', 3100: 'diet', 3101: 'worldly', 3102: 'mickey', 3103: 'kentucky', 3104: 'avalanche', 3105: 'bugs', 3106: 'sat', 3107: 'bet', 3108: 'versus', 3109: 'ralph_wiggum:', 3110: 'stacey', 3111: 'ling', 3112: 'billingsley', 3113: 'tired', 3114: 'two-drink', 3115: 'screw', 3116: 'weirded-out', 3117: 'passenger', 3118: 'pridesters:', 3119: 'landlord', 3120: 'novel', 3121: 'inspired', 3122: 'two', 3123: 'equal', 3124: 'fellow', 3125: 'dangerous', 3126: 'wally', 3127: 'hurts', 3128: 'mostly', 3129: 'legs', 3130: 'muhammad', 3131: 'michael', 3132: 'ow', 3133: 'large', 3134: 'stiffening', 3135: 'beautiful', 3136: 'crooks', 3137: '_hooper:', 3138: 'jimmy', 3139: 'steampunk', 3140: 'eating', 3141: 'alls', 3142: 'awake', 3143: 'wrapped', 3144: 'figure', 3145: 'pushes', 3146: 'sunk', 3147: 'crystal', 3148: 'flynt', 3149: 'tap', 3150: 'finishing', 3151: 'daddy', 3152: "you're", 3153: 'instrument', 3154: 'spine', 3155: 'rain', 3156: 'without:', 3157: 'am', 3158: "family's", 3159: 'takeaway', 3160: "mopin'", 3161: 'wealthy', 3162: 'rainier_wolfcastle:', 3163: 'nervous', 3164: 'fierce', 3165: 'part', 3166: 'one-hour', 3167: 'convenient', 3168: 'rummy', 3169: 'haircuts', 3170: 'peter_buck:', 3171: 'pusillanimous', 3172: 'flat', 3173: 'defected', 3174: 'compels', 3175: 'verticality', 3176: 'hungry', 3177: "daughter's", 3178: '||right_paren||', 3179: 'dexterous', 3180: 'e', 3181: 'feels', 3182: 'thinking', 3183: 'meditative', 3184: "coaster's", 3185: 'forgets', 3186: 'fireball', 3187: 'foil', 3188: 'maude', 3189: 'giving', 3190: 'lushmore', 3191: 'stand', 3192: 'thunder', 3193: 'beef', 3194: 'south', 3195: 'ecru', 3196: 'fad', 3197: 'assassination', 3198: 'glyco-load', 3199: 'cookies', 3200: 'considering', 3201: 'hell', 3202: 'background', 3203: 'leprechaun', 3204: 'snorts', 3205: 'trouble', 3206: 'twin', 3207: 'aah', 3208: 'committing', 3209: 'slip', 3210: 'heh', 3211: 'michelin', 3212: 'mac-who', 3213: 'moxie', 3214: 'charity', 3215: 'intruding', 3216: 'breaks', 3217: 'sucking', 3218: 'clothes', 3219: 'inserts', 3220: 'stewart', 3221: 'dispenser', 3222: 'over-pronouncing', 3223: 'uninhibited', 3224: 'overstressed', 3225: 'tipsy', 3226: 'wire', 3227: 'swan', 3228: 'un-sults', 3229: 'horribilis', 3230: 'murderously', 3231: 'buried', 3232: 'scotch', 3233: 'gabriel', 3234: 'average', 3235: 'string', 3236: '-ry', 3237: 'until', 3238: '35', 3239: '250', 3240: 'signed', 3241: 'len-ny', 3242: "payin'", 3243: 'prompting', 3244: 'giggles', 3245: 'reaction', 3246: 'counting', 3247: 'drinking:', 3248: 'cameras', 3249: 'droning', 3250: 'grin', 3251: 'nã£o', 3252: 'stage', 3253: 'wave', 3254: 'boat', 3255: 'lobster-based', 3256: "tap-pullin'", 3257: 'hotline', 3258: 'brief', 3259: 'stonewall', 3260: 'forbids', 3261: 'effect', 3262: 'vegas', 3263: 'back', 3264: 'hunka', 3265: 'worry', 3266: 'often', 3267: 'lifts', 3268: 'tentative', 3269: 'cappuccino', 3270: 'strawberry', 3271: 'lungs', 3272: 'coffee', 3273: 'but', 3274: 'assent', 3275: 'fbi_agent:', 3276: "they've", 3277: 'tomorrow', 3278: 'nine', 3279: 'beaumont', 3280: 'live', 3281: 'man_at_bar:', 3282: 'maitre', 3283: 'founded', 3284: "'", 3285: 'queer', 3286: 'ore', 3287: 'foot', 3288: 'ground', 3289: 'dating', 3290: 'cocks', 3291: 'countryman', 3292: 'investment', 3293: 'stones', 3294: 'singer', 3295: 'apartment', 3296: 'annie', 3297: 'fund', 3298: 'fritz:', 3299: 'espousing', 3300: 'goo', 3301: 'chuckles', 3302: 'tongue', 3303: 'bank', 3304: 'haplessly', 3305: 'reality', 3306: 'occasional', 3307: 'rice', 3308: 'lists', 3309: 'fights', 3310: 'intakes', 3311: 'portuguese', 3312: 'danny', 3313: 'grieving', 3314: 'adult_bart:', 3315: 'poster', 3316: 'fireworks', 3317: 'bathtub', 3318: 'clap', 3319: "bart'd", 3320: 'windex', 3321: "c'mom", 3322: 'tv-station_announcer:', 3323: 'rome', 3324: 'tonic', 3325: 'entertainer', 3326: 'ingrates', 3327: 'spooky', 3328: 'birth', 3329: 'forecast', 3330: 'with', 3331: "askin'", 3332: 'bartending', 3333: 'dearest', 3334: 'patty', 3335: 'hold', 3336: 'made', 3337: 'although', 3338: 'reserve', 3339: 'shooting', 3340: 'designer', 3341: 'lloyd', 3342: "chewin'", 3343: 'suspicious', 3344: 'eyed', 3345: 'snap', 3346: 'nervously', 3347: 'saved', 3348: 'jockey', 3349: 'cure', 3350: 'pinball', 3351: 'harv:', 3352: 'sotto', 3353: 'tooth', 3354: 'lisa_simpson:', 3355: 'painless', 3356: 'louder', 3357: 'license', 3358: 'scatter', 3359: 'hundred', 3360: 'um', 3361: "'cept", 3362: 'broadway', 3363: 'increasingly', 3364: 'face', 3365: 'bar:', 3366: "show's", 3367: 'eighteen', 3368: 'sees', 3369: 'zack', 3370: 'bite', 3371: 'partners', 3372: 'cases', 3373: 'jerry', 3374: 'if', 3375: 'nurse', 3376: 'majesty', 3377: 'bartender', 3378: 'yells', 3379: 'jogging', 3380: 'disguised', 3381: 'parrot', 3382: 'unintelligent', 3383: 'innocence', 3384: 'frankie', 3385: 'sec', 3386: 'fastest', 3387: 'aerosmith', 3388: 'dear', 3389: 'stuff', 3390: 'eww', 3391: 'bow', 3392: 'ball', 3393: "yesterday's", 3394: 'canyonero', 3395: 'hero-phobia', 3396: 'closed', 3397: 'hillary', 3398: 'promise', 3399: 'spread', 3400: 'soir', 3401: 'store', 3402: 'easy', 3403: 'freeze', 3404: 'jig', 3405: 'certified', 3406: 'busiest', 3407: 'fence', 3408: 'predecessor', 3409: 'sexy', 3410: 'janette', 3411: 'tuborg', 3412: "thing's", 3413: 'crappy', 3414: "wasn't", 3415: 'rupert_murdoch:', 3416: "how'd", 3417: 'hook', 3418: 'mariah', 3419: 'cap', 3420: 'thoughtless', 3421: 'emotional', 3422: 'belly-aching', 3423: 'potatoes', 3424: 'walks', 3425: '&', 3426: 'oooh', 3427: 'dirty', 3428: 'fumigated', 3429: 'excited', 3430: 'new', 3431: 'undies', 3432: 'mill', 3433: 'corporation', 3434: '||semicolon||', 3435: 'silence', 3436: 'bury', 3437: 'bag', 3438: 'sexual', 3439: 'glamour', 3440: 'perfected', 3441: "america's", 3442: 'training', 3443: 'filed', 3444: 'slaves', 3445: 'duffman:', 3446: 'sit', 3447: 'society', 3448: 'blank', 3449: 'cuz', 3450: 'gore', 3451: 'avec', 3452: 'ugly', 3453: 'doll', 3454: 'splattered', 3455: 'reflected', 3456: '_timothy_lovejoy:', 3457: 'fink', 3458: 'excuses', 3459: 'pall', 3460: 'ugh', 3461: 'kermit', 3462: 'older', 3463: 'goodbye', 3464: 'pause', 3465: 'basement', 3466: 'however', 3467: 'teenage_homer:', 3468: 'mount', 3469: 'reasonable', 3470: 'sodas', 3471: 'cent', 3472: 'hooked', 3473: 'loss', 3474: 'gags', 3475: 'unsourced', 3476: 'awful', 3477: 'enemy', 3478: 'amount', 3479: 'yourself', 3480: 'speak', 3481: 'judge_snyder:', 3482: 'liable', 3483: 'jelly', 3484: 'darkness', 3485: 'did', 3486: 'richer', 3487: 'play', 3488: 'were', 3489: 'horrified', 3490: 'juan', 3491: 'radioactive', 3492: 'waltz', 3493: 'tree_hoper:', 3494: 'grab', 3495: 'cattle', 3496: 'experienced', 3497: 'funeral', 3498: 'closer', 3499: 'feat', 3500: 'examples', 3501: 'emotion', 3502: 'bauer', 3503: 'pretzels', 3504: 'which', 3505: 'drapes', 3506: "boy's", 3507: 'woman', 3508: "man'd", 3509: 'guys', 3510: 'enough', 3511: 'contract', 3512: 'youngsters', 3513: 'anything', 3514: 'percent', 3515: 'telling', 3516: 'beefs', 3517: 'otherwise', 3518: 'here-here-here', 3519: 'read:', 3520: 'cars', 3521: 'accident', 3522: 'peppy', 3523: 'penmanship', 3524: 'proves', 3525: 'dictating', 3526: 'enter', 3527: 'spender', 3528: "lefty's", 3529: 'stagehand:', 3530: 'fragile', 3531: 'feast', 3532: 'juke', 3533: 'occurred', 3534: 'standards', 3535: 'boyhood', 3536: 'squeeze', 3537: 'thinks', 3538: 'hooray', 3539: 'rats', 3540: 'and/or', 3541: 'washer', 3542: 'mid-seventies', 3543: 'stretches', 3544: 'lear', 3545: 'year', 3546: 'example', 3547: 'laughter', 3548: "i'm-so-stupid", 3549: 'etc', 3550: 'neck', 3551: 'till', 3552: 'eliminate', 3553: 'offa', 3554: 'broken', 3555: 'parasol', 3556: 'club', 3557: 'cecil_terwilliger:', 3558: 'pretty', 3559: 'nick', 3560: 'wobble', 3561: 'someday', 3562: 'had', 3563: "we're", 3564: 'field', 3565: 'finest', 3566: 'almond', 3567: 'corpses', 3568: 'raccoons', 3569: 'gibson', 3570: 'swings', 3571: "who's", 3572: 'deacon', 3573: 'poulet', 3574: 'sponsor', 3575: 'available', 3576: 'gentlemen', 3577: 'tied', 3578: 'bird', 3579: 'fourteen:', 3580: 'videotaped', 3581: 'knit', 3582: 'tigers', 3583: 'guttural', 3584: 'cab_driver:', 3585: "bashir's", 3586: 'of', 3587: 'floor', 3588: 'opens', 3589: 'hoped', 3590: 'muffled', 3591: 'sequel', 3592: 'try', 3593: 'agent_johnson:', 3594: 'ran', 3595: 'perplexed', 3596: 'join', 3597: 'occurs', 3598: 'chastity', 3599: 'step', 3600: 'various', 3601: 'trusted', 3602: 'ultimate', 3603: 'securities', 3604: 'guest', 3605: 'gear-head', 3606: 'lizard', 3607: 'talk', 3608: 'somewhere', 3609: 'bobo', 3610: "secret's", 3611: 'getaway', 3612: 'befriend', 3613: 'author', 3614: 'popular', 3615: 'stick', 3616: 'legend', 3617: 'changed', 3618: 'plucked', 3619: 'ape-like', 3620: 'scout', 3621: 'therapy', 3622: 'november', 3623: 'ruled', 3624: 'botanical', 3625: 'ivy-covered', 3626: "valentine's", 3627: 'backing', 3628: 'text', 3629: 'who', 3630: 'enjoys', 3631: 'boyfriend', 3632: 'underbridge', 3633: 'cozies', 3634: 'lime', 3635: 'padres', 3636: 'joey', 3637: 'us', 3638: 'frankly', 3639: 'walk', 3640: 'restaurants', 3641: 'mathis', 3642: 'clearing', 3643: 'late', 3644: 'poke', 3645: 'woooooo', 3646: 'moved', 3647: 'grenky', 3648: 'affectations', 3649: "haven't", 3650: 'looser', 3651: 'kinds', 3652: 'nonsense', 3653: 'dying', 3654: 'ho-ly', 3655: 'whup', 3656: "where'd", 3657: 'one', 3658: 'lot', 3659: 'hems', 3660: 'familiar', 3661: 'improv', 3662: 'fixed', 3663: 'tune', 3664: 'bras', 3665: 'enhance', 3666: 'young_barfly:', 3667: 'saturday', 3668: 'agents', 3669: 'knowing', 3670: 'together', 3671: 'pizza', 3672: 'twenty-two', 3673: 'clone', 3674: 'accounta', 3675: 'control', 3676: 'nickels', 3677: 'vomit', 3678: 'aiden', 3679: 'championship', 3680: "money's", 3681: 'chance', 3682: 'skydiving', 3683: 'clams', 3684: 'touches', 3685: 'having', 3686: 'cute', 3687: 'arrest', 3688: 'chapter', 3689: 'ref', 3690: 'decided', 3691: 'stood', 3692: 'these', 3693: "narratin'", 3694: 'bronco', 3695: 'chief_wiggum:', 3696: "something's", 3697: 'soft', 3698: 'teach', 3699: 'persia', 3700: 'witty', 3701: 'pets', 3702: 'ocean', 3703: 'tail', 3704: 'patting', 3705: 'wrap', 3706: 'poor', 3707: 's-a-u-r-c-e', 3708: 'tv_wife:', 3709: 'harm', 3710: 'enemies', 3711: 'boston', 3712: 'exquisite', 3713: 'fishing', 3714: 'allowed', 3715: 'needed', 3716: 'began', 3717: 'luckiest', 3718: 'stepped', 3719: 'delivery', 3720: 'nelson_muntz:', 3721: 'duh', 3722: 'sweat', 3723: 'languages', 3724: 'trench', 3725: 'bindle', 3726: 'low-blow', 3727: 'retired', 3728: 'gals', 3729: 'squadron', 3730: 'tokens', 3731: 'flush', 3732: 'correction', 3733: 'casting', 3734: 'hanging', 3735: 'seething', 3736: 'tickets', 3737: 'germans', 3738: 'duke', 3739: 'flack', 3740: 'grunt', 3741: 'moonlight', 3742: 'life-partner', 3743: 'domed', 3744: 'car:', 3745: 'alcoholic', 3746: 'smiling', 3747: "mother's", 3748: 'voice_on_transmitter:', 3749: 'luck', 3750: 'ha-ha', 3751: '8', 3752: 'america', 3753: 'gol-dangit', 3754: 'ate', 3755: 'bear', 3756: 'lowering', 3757: "fans'll", 3758: 'pleading', 3759: 'modestly', 3760: 'fight', 3761: 'nineteen', 3762: 'obama', 3763: 'vacations', 3764: 'strategy', 3765: "nixon's", 3766: 'have', 3767: 'sec_agent_#1:', 3768: 'audience:', 3769: 'fighting', 3770: 'toss', 3771: 'hero', 3772: 'beer:', 3773: 'hardy', 3774: 'mini-beret', 3775: 'midge', 3776: 'smuggled', 3777: 'oblivious', 3778: 'artist', 3779: 'someplace', 3780: 'answers', 3781: 'watched', 3782: 'lib', 3783: 'capitalists', 3784: 'cosmetics', 3785: 'xx', 3786: 'pair', 3787: 'idea', 3788: 'slot', 3789: 'blood', 3790: 'horns', 3791: 'minors', 3792: 'leftover', 3793: 'superpower', 3794: 'air', 3795: 'mistresses', 3796: 'righ', 3797: 'thank', 3798: 'stu', 3799: 'yoo', 3800: 'might', 3801: 'lift', 3802: 'lights', 3803: 'arm-pittish', 3804: 'quickly', 3805: 'reporter', 3806: 'sam:', 3807: 'eager', 3808: 'huhza', 3809: 'exit', 3810: 'drunkening', 3811: 'disturbance', 3812: 'albeit', 3813: 'forget-me-shot', 3814: 'inches', 3815: 'allow', 3816: '10:15', 3817: 'perch', 3818: 'spare', 3819: 'graveyard', 3820: 'hope', 3821: 'penny', 3822: 'sending', 3823: 'dirt', 3824: 'helicopter', 3825: 'happier', 3826: 'suicide', 3827: 'gentle', 3828: 'goods', 3829: 'ripcord', 3830: 'coney', 3831: 'truck_driver:', 3832: 'skinheads', 3833: 'service', 3834: 'civilization', 3835: 'knows', 3836: 'missed', 3837: 'force', 3838: "beggin'", 3839: 'blade', 3840: 'test-lady', 3841: 'roof', 3842: 'sadistic_barfly:', 3843: 'sometimes', 3844: 'larry:', 3845: 'hate-hugs', 3846: 'commanding', 3847: 'wittgenstein', 3848: 'cutie', 3849: 'charged', 3850: 'cotton', 3851: 'score', 3852: 'shut', 3853: 'yup', 3854: "goin'", 3855: "pressure's", 3856: 'radical', 3857: 'reluctant', 3858: 'they', 3859: "this'll", 3860: 'dallas', 3861: 'mcclure', 3862: 'reluctantly', 3863: 'candidate', 3864: 'ghouls', 3865: 'tv', 3866: 'rip', 3867: 'coma', 3868: 'louisiana', 3869: 'thoughtfully', 3870: 'mafia', 3871: 'mm', 3872: 'playful', 3873: "someone's", 3874: 'gangrene', 3875: 'victorious', 3876: 'piece', 3877: 'tear', 3878: 'booze-bags', 3879: 'selective', 3880: 'super-tough', 3881: 'women', 3882: 'crinkly', 3883: 'drives', 3884: "'im", 3885: 'ceremony', 3886: 'page', 3887: 'fever', 3888: 'even', 3889: "gettin'", 3890: 'triple-sec', 3891: 'reopen', 3892: 'homie', 3893: 'dame', 3894: 'taken', 3895: 'comic', 3896: 'fresco', 3897: 'face-macer', 3898: 'teen', 3899: 'winning', 3900: 'still', 3901: 'shock', 3902: 'acronyms', 3903: 'bills', 3904: 'bake', 3905: 'prayers', 3906: 'squeals', 3907: 'killarney', 3908: 'shopping', 3909: 'mini-dumpsters', 3910: 'bono:', 3911: 'spotting', 3912: 'digging', 3913: 'nothing', 3914: 'drederick', 3915: 'junior', 3916: 'crushed', 3917: 'hammy', 3918: 'sang', 3919: 'guilt', 3920: 'extra', 3921: 'yourse', 3922: 'reentering', 3923: 'caught', 3924: 'windshield', 3925: "spyin'", 3926: 'good', 3927: 'susie-q', 3928: 'k-zug', 3929: 'feel', 3930: 'temple', 3931: 'greatly', 3932: 'keeps', 3933: 'cents', 3934: 'kings', 3935: 'presidents', 3936: "ya'", 3937: 'repay', 3938: 'fiiiiile', 3939: "bartender's", 3940: 'behind', 3941: 'appropriate', 3942: 'special', 3943: 'sadder', 3944: 'paste', 3945: 'divorced', 3946: 'homers', 3947: 'prince', 3948: 'toward', 3949: 'hockey-fight', 3950: 'skirt', 3951: 'unkempt', 3952: 'evergreen', 3953: 'honey', 3954: 'slyly', 3955: 'scared', 3956: 'buffalo', 3957: 'sharps', 3958: 'cruel', 3959: 'guff', 3960: 'alfred', 3961: 'chug', 3962: 'insightful', 3963: 'elite', 3964: 'age', 3965: 'embarrassed', 3966: 'depressed', 3967: 'quimbys:', 3968: 'tries', 3969: 'film', 3970: 'prettied', 3971: 'taps', 3972: 'relationship', 3973: 'collateral', 3974: 'lodge', 3975: 'principal', 3976: 'picture', 3977: 'lighting', 3978: 'chauffeur:', 3979: 'invisible', 3980: 'composer', 3981: 'maya', 3982: 'hilton', 3983: 'champion', 3984: 'weeks', 3985: 'month', 3986: 'poking', 3987: 'yammering', 3988: 'blame', 3989: 'exasperated', 3990: 'professor_jonathan_frink:', 3991: 'terrible', 3992: 'between', 3993: 'earlier', 3994: 'behavior', 3995: 'amiable', 3996: 'three-man', 3997: 'weak', 3998: 'tofu', 3999: 'medieval', 4000: 'killing', 4001: 'button-pusher', 4002: 'rickles', 4003: 'loved', 4004: 'strategizing', 4005: 'said', 4006: 'nahasapeemapetilon', 4007: 'lookalike:', 4008: 'influence', 4009: 'grants', 4010: 'wham', 4011: 'hammock', 4012: 'history', 4013: 'betcha', 4014: "usin'", 4015: 'eats', 4016: "singin'", 4017: 'partly', 4018: 'failed', 4019: 'hunger', 4020: 'grandmother', 4021: 'warmly', 4022: 'moments', 4023: 'engine', 4024: "it'd", 4025: 'grimly', 4026: 'butts', 4027: "renovatin'", 4028: 'jã¤germeister', 4029: 'soul', 4030: 'suspiciously', 4031: 'scrutinizing', 4032: 'steinbrenner', 4033: 'treasure', 4034: 'saint', 4035: 'robot', 4036: 'those', 4037: 'pants', 4038: 'husband', 4039: 'caused', 4040: 'liven', 4041: 'peabody', 4042: 'snide', 4043: 'musta', 4044: 'beach', 4045: 'gardens', 4046: 'perking', 4047: 'serious', 4048: 'devastated', 4049: 'considers', 4050: 'glen', 4051: 'manatee', 4052: 'handed', 4053: 'presumir', 4054: 'broncos', 4055: 'quero', 4056: 'coaster', 4057: "hell's", 4058: 'dramatically', 4059: 'ne', 4060: 'easier', 4061: "makin'", 4062: 'dive', 4063: 'slobbo', 4064: 'universe', 4065: 'recruiter', 4066: 'congratulations', 4067: 'captain:', 4068: 'peppers', 4069: 'pulling', 4070: 'ninety-six', 4071: 'watered-down', 4072: 'iddilies', 4073: 'wacky', 4074: "doctor's", 4075: 'eco-fraud', 4076: 'rosey', 4077: 'then:', 4078: 'swigmore', 4079: "kiddin'", 4080: 'cockroach', 4081: 'irishman', 4082: 'connor-politan', 4083: 'warren', 4084: 'mistakes', 4085: 'jerk', 4086: 'partially', 4087: 'skoal', 4088: 'romantic', 4089: 'pepto-bismol', 4090: 'flashbacks', 4091: "'your", 4092: "it's", 4093: 'million', 4094: 'pointedly', 4095: 'guzzles', 4096: 'granted', 4097: 'mix', 4098: ':', 4099: 'blaze', 4100: 'dennis', 4101: 'masks', 4102: 'narrator:', 4103: 'nein', 4104: 'declan', 4105: 'cerebral', 4106: 'cannot', 4107: 'fourth', 4108: 'half-day', 4109: 'pronto', 4110: 'breathalyzer', 4111: 'unbelievably', 4112: 'preparation', 4113: 'nonchalantly', 4114: '21', 4115: "playin'", 4116: 'mug', 4117: 'peach', 4118: 'begin', 4119: 'inspire', 4120: 'christmas', 4121: 'learn', 4122: 'up', 4123: 'evils', 4124: 'wears', 4125: 'bragging', 4126: 'barkeeps', 4127: 'logos', 4128: "costume's", 4129: 'whatever', 4130: 'shutting', 4131: 'anyhow', 4132: 'adequate', 4133: 'permitting', 4134: 'aggravazes', 4135: 'toledo', 4136: 'municipal', 4137: "department's", 4138: 'impending', 4139: 'mike_mills:', 4140: '7-year-old_brockman:', 4141: 'winner', 4142: 'cavern', 4143: 'broke', 4144: 'characteristic', 4145: 'waitress', 4146: 'crummy', 4147: 'bartenders', 4148: 'anarchy', 4149: 'sixty-five', 4150: 'scream', 4151: 'unable', 4152: 'spectacular', 4153: 'family', 4154: 'going', 4155: 'ned', 4156: 'enthusiastically', 4157: 'actors', 4158: 'nerd', 4159: 'grunts', 4160: 'sturdy', 4161: 'bedbugs', 4162: 'bleak', 4163: 'bush', 4164: 'pocket', 4165: 'deserve', 4166: 'pancakes', 4167: 'obsessive-compulsive', 4168: 'grand', 4169: 'punch', 4170: 'fresh', 4171: 'simpsons', 4172: 'ping-pong', 4173: 'passports', 4174: 'honor', 4175: 'news', 4176: 'yap', 4177: 'exactly', 4178: 'morlocks', 4179: 'employment', 4180: 'senators:', 4181: 'contemplated', 4182: 'beauty', 4183: 'remember', 4184: 'forgiven', 4185: 'chilly', 4186: 'the_rich_texan:', 4187: 'skinny', 4188: 'ram', 4189: 'crumble', 4190: 'both', 4191: 'longest', 4192: 'tin', 4193: 'mckinley', 4194: 'deny', 4195: 'effects', 4196: 'knock-up', 4197: 'a-b-', 4198: 'bull', 4199: 'e-z', 4200: 'grandkids', 4201: 'grope', 4202: 'heavens', 4203: 'break', 4204: "kid's", 4205: 'understanding', 4206: 'mmmm', 4207: 'weight', 4208: 'solely', 4209: 'handling', 4210: 'items', 4211: 'filth', 4212: 'voted', 4213: 'reserved', 4214: 'lease', 4215: 'aged', 4216: 'meaning', 4217: 'anybody', 4218: 'midnight', 4219: 'kemi', 4220: 'urinal', 4221: 'holy', 4222: 'attractive_woman_#2:', 4223: 'roll', 4224: 'coins', 4225: 'notices', 4226: 'vin', 4227: 'potato', 4228: 'lookalikes', 4229: '_montgomery_burns:', 4230: 'right-handed', 4231: '$42', 4232: 'app', 4233: 'doppler', 4234: 'herself', 4235: 'm', 4236: 'comfortable', 4237: 'wade_boggs:', 4238: 'assumed', 4239: 'prefer', 4240: 'creeps', 4241: 'teeth', 4242: 'mine', 4243: 'infor', 4244: 'wide', 4245: 'wordloaf', 4246: 'jasper_beardly:', 4247: 'keeping', 4248: 'nards', 4249: 'irish', 4250: 'must', 4251: 'vehicle', 4252: 'ho-la', 4253: 'tears', 4254: 'state', 4255: 'take-back', 4256: 'pack', 4257: 'operation', 4258: 'period', 4259: 'delivery_boy:', 4260: 'anyhoo', 4261: 'fifth', 4262: 'spelling', 4263: 'served', 4264: 'buzziness', 4265: 'closes', 4266: 'settled', 4267: "wino's", 4268: 'rector', 4269: 'jackass', 4270: "tellin'", 4271: "o'", 4272: 'tearfully', 4273: 'maya:', 4274: 'test-', 4275: 'quietly', 4276: 'smug', 4277: 'liver', 4278: 'lighter', 4279: 'meeting', 4280: 'favor', 4281: 'survive', 4282: 'unrelated', 4283: 'college', 4284: 'smiled', 4285: 'inclination', 4286: 'pass', 4287: 'princess', 4288: 'spitting', 4289: 'afford', 4290: 'sharity', 4291: 'mindless', 4292: 'facebook', 4293: 'created', 4294: 'pope', 4295: 'bugging', 4296: 'para', 4297: 'sweet', 4298: 'bachelorhood', 4299: 'militia', 4300: 'including', 4301: 'chili', 4302: 'iranian', 4303: 'wienerschnitzel', 4304: 'share', 4305: 'blokes', 4306: 'carlson', 4307: 'pulled', 4308: 'sly', 4309: 'theory', 4310: 'excuse', 4311: 'ivory', 4312: 'olive', 4313: 'busy', 4314: 'haws', 4315: 'flaking', 4316: 'needs', 4317: 'gambler', 4318: 'pro', 4319: 'regret', 4320: 'against', 4321: 'whenever', 4322: "hawkin'", 4323: 'amazing', 4324: 'juice', 4325: 'loud', 4326: 'cheerier', 4327: 'confidential', 4328: 'cause', 4329: 'polenta', 4330: 'my', 4331: 'jay_leno:', 4332: 'please/', 4333: 'slogan', 4334: 'clenched', 4335: 'doreen', 4336: 'swallowed', 4337: 'gently', 4338: 'you', 4339: 'latour', 4340: 'delicate', 4341: 'firmly', 4342: 'moesy', 4343: 'lenny:', 4344: 'hyahh', 4345: 'ah', 4346: 'polish', 4347: 'table', 4348: 'upgrade', 4349: 'shifty', 4350: "dad's", 4351: 'speech', 4352: 'contemplates', 4353: 'movies', 4354: 'kang:', 4355: 'hah', 4356: 'fold', 4357: 'credit', 4358: 'protecting', 4359: 'losers', 4360: 'under', 4361: 'schnapps', 4362: 'level', 4363: "we'd", 4364: "they're", 4365: 'seymour', 4366: 'killjoy', 4367: 'curious', 4368: 'belong', 4369: 'clientele', 4370: 'yogurt', 4371: 'bastard', 4372: 'theme', 4373: 'non-american', 4374: 'woman_bystander:', 4375: 'hourly', 4376: 'supervising', 4377: 'priceless', 4378: 'defensive', 4379: 'rent', 4380: 'tabs', 4381: 'santeria', 4382: 'wiggum', 4383: 'nor', 4384: 'pre-recorded', 4385: "number's", 4386: 'lise:', 4387: 'gimmicks', 4388: 'cauliflower', 4389: 'selfish', 4390: 'moon-bounce', 4391: 'howya', 4392: 'unfresh', 4393: 'reason', 4394: 'depressing', 4395: 'drains', 4396: 'tavern', 4397: 'newest', 4398: 'jeff_gordon:', 4399: 'reciting', 4400: 'superhero', 4401: 'devils:', 4402: "must've", 4403: 'greystash', 4404: 'chips', 4405: 'arms', 4406: 'awww', 4407: 'benjamin', 4408: 'blimp', 4409: 'barflies:', 4410: 'ragtime', 4411: 'souped', 4412: 'campaign', 4413: 'imaginary', 4414: 'clips', 4415: 'hers', 4416: 'station', 4417: 'bunion', 4418: 'pre-columbian', 4419: 'placing', 4420: 'polite', 4421: "one's", 4422: "stealin'", 4423: 'blissful', 4424: 'classy', 4425: 'fixes', 4426: 'blooded', 4427: 'memories', 4428: 'competitive', 4429: 'bowled', 4430: 'handwriting', 4431: 'taste', 4432: 'than', 4433: 'vulnerable', 4434: 'loafers', 4435: 'department', 4436: 'diminish', 4437: 'outstanding', 4438: 'plotz', 4439: "it'll", 4440: 'taunting', 4441: 'payments', 4442: 'scrutinizes', 4443: 'mither', 4444: 'make', 4445: 'wildfever', 4446: 'cocking', 4447: 'hi', 4448: 'wazoo', 4449: 'frescas', 4450: 'things', 4451: 'thirty-three', 4452: 'unlocked', 4453: 'pillows', 4454: 'christian', 4455: 'pathetic', 4456: 'wheels', 4457: 'woe:', 4458: 'adjust', 4459: '||quotation_mark||', 4460: 'aside', 4461: 'meal', 4462: 'crazy', 4463: 'unhook', 4464: 'lennyy', 4465: 'ball-sized', 4466: 'reviews', 4467: 'mole', 4468: 'bad', 4469: 'rings', 4470: 'scientists', 4471: 'teriyaki', 4472: 'underwear', 4473: 'foam', 4474: 'dana_scully:', 4475: 'democrats', 4476: 'spacey', 4477: 'appealing', 4478: 'email', 4479: 'per', 4480: 'community', 4481: 'nigerian', 4482: 'self-satisfied', 4483: 'feelings', 4484: 'hangover', 4485: 'distance', 4486: "disrobin'", 4487: 'stagey', 4488: 'hosting', 4489: 'sensible', 4490: "s'pose", 4491: 'payback', 4492: 'wangs', 4493: 'dozen', 4494: 'innocent', 4495: 'island', 4496: 'stab', 4497: 'duel', 4498: 'breaking', 4499: 'abe', 4500: 'ears', 4501: 'judges', 4502: 'scare', 4503: '530', 4504: 'movie', 4505: 'accent', 4506: 'damn', 4507: 'guy', 4508: "sayin'", 4509: 'clock', 4510: 'crayon', 4511: 'weekly', 4512: 'self-centered', 4513: 'activity', 4514: 'golf', 4515: 'karaoke_machine:', 4516: 'press', 4517: 'war', 4518: 'offshoot', 4519: 'flaming', 4520: 'murdered', 4521: 'femininity', 4522: 'ways', 4523: 'bagged', 4524: 'aghast', 4525: 'dog', 4526: 'home', 4527: 'football_announcer:', 4528: 'sampler', 4529: 'intention', 4530: 'necklace', 4531: 'kidneys', 4532: 'ho', 4533: "dyin'", 4534: 'lips', 4535: 'likes', 4536: 'shaved', 4537: 'chick', 4538: 'st', 4539: 'wrote', 4540: 'wrecking', 4541: 'trail', 4542: 'coward', 4543: 'stan', 4544: 'seamstress', 4545: 'buying', 4546: 'headhunters', 4547: 'gift', 4548: 'moe_szyslak:', 4549: 'surprise', 4550: 'dregs', 4551: 'barstools', 4552: 'mint', 4553: 'supermarket', 4554: 'and-and', 4555: 'hair', 4556: 'power', 4557: 'single-mindedness', 4558: "tatum'll", 4559: 'yelling', 4560: 'grampa', 4561: 'reminded', 4562: 'sudden', 4563: 'wells', 4564: 'homer_doubles:', 4565: 'sniffing', 4566: 'weary', 4567: 'seas', 4568: 'painting', 4569: 'office', 4570: 'deliberate', 4571: 'judge', 4572: 'lied', 4573: 'smooth', 4574: 'protestantism', 4575: 'socratic', 4576: 'nbc', 4577: 'yeah', 4578: 'promised', 4579: 'automobiles', 4580: 'specific', 4581: "doesn't", 4582: "duff's", 4583: 'follow', 4584: 'dateline', 4585: 'whisper', 4586: 'part-time', 4587: 'philosophic', 4588: 'arse', 4589: 'inquiries', 4590: 'checking', 4591: 'guinea', 4592: 'term', 4593: 'edgy', 4594: 'craft', 4595: 'ahh', 4596: 'ruint', 4597: 'old_jewish_man:', 4598: 'bannister', 4599: 'handsome', 4600: 'hail', 4601: 'ripping', 4602: 'retain', 4603: 'bride', 4604: 'eddie', 4605: 'hans:', 4606: 'absentminded', 4607: 'wildest', 4608: 'numeral', 4609: 'quadruple-sec', 4610: 'mob', 4611: 'tragedy', 4612: 'die', 4613: 'god', 4614: 'twenty-four', 4615: 'charming', 4616: 'falcons', 4617: 'leonard', 4618: 'billion', 4619: 'cage', 4620: 'for', 4621: 'trapped', 4622: 'barney-guarding', 4623: 'satisfied', 4624: 'terminated', 4625: 'jeers', 4626: 'golden', 4627: 'squirrel', 4628: "donatin'", 4629: 'distributor', 4630: 'sail', 4631: 'hug', 4632: 'housing', 4633: 'delete', 4634: 'noticing', 4635: 'material', 4636: 'touched', 4637: 'noose', 4638: 'naively', 4639: 'wasted', 4640: 'handler', 4641: "burnin'", 4642: 'aidens', 4643: 'maxed', 4644: 'scully', 4645: 'witches', 4646: 'folk', 4647: 'skunk', 4648: 'hampstead-on-cecil-cecil', 4649: 'pain', 4650: 'faced', 4651: 'magazine', 4652: 'nah', 4653: "messin'", 4654: 'edelbrock', 4655: 'safe', 4656: 'abandon', 4657: 'manchego', 4658: "spiffin'", 4659: 'hidden', 4660: 'maiden', 4661: 'lobster-politans', 4662: "heat's", 4663: 'finger', 4664: 'zoomed', 4665: 'eight-year-old', 4666: 'bubble', 4667: 'hunting', 4668: 'ew', 4669: 'finished', 4670: 'strokkur', 4671: 'compromise:', 4672: 'wizard', 4673: 'situation', 4674: 'vacation', 4675: 'space-time', 4676: 'boring', 4677: 'dies', 4678: 'homeland', 4679: 'pronounce', 4680: 'key', 4681: 'philosophical', 4682: 'due', 4683: 'scrape', 4684: "how's", 4685: 'ron', 4686: 'pawed', 4687: 'bright', 4688: 'doctor', 4689: 'sits', 4690: 'international', 4691: 'owe', 4692: 'solid', 4693: 'lord', 4694: 'snake-handler', 4695: "couldn't", 4696: 'metal', 4697: "team's", 4698: 'cutest', 4699: "ladies'", 4700: 'wayne', 4701: 'd', 4702: 'course', 4703: 'dennis_kucinich:', 4704: 'sandwich', 4705: 'maintenance', 4706: 'norway', 4707: 'tanking', 4708: 'coach:', 4709: 'holidays', 4710: 'lincoln', 4711: 'rutabaga', 4712: 'uh-huh', 4713: 'punching', 4714: 'fast-paced', 4715: 'load', 4716: 'time', 4717: 'robin', 4718: 'william', 4719: "summer's", 4720: 'media', 4721: 'filled', 4722: 'freedom', 4723: 'ivana', 4724: 'lofty', 4725: 'canoodling', 4726: 'mrs', 4727: 'pride', 4728: 'perfect', 4729: 'ziffcorp', 4730: 'odd', 4731: 'door', 4732: 'smugglers', 4733: 'perfume', 4734: 'stock', 4735: 'stunned', 4736: 'furry', 4737: 'species', 4738: 'p-k', 4739: 'dead', 4740: 'lone', 4741: 'eh', 4742: 'renders', 4743: 'shtick', 4744: 'wars', 4745: "depressin'", 4746: 'defiantly', 4747: 'already', 4748: 'mayor', 4749: 'proudly', 4750: 'reminds', 4751: "moe's_thoughts:", 4752: 'highball', 4753: 'young_marge:', 4754: 'chew', 4755: 'comment', 4756: 'texas', 4757: 'allegiance', 4758: 'aww', 4759: 'single', 4760: '||period||', 4761: 'dreamily', 4762: 'world', 4763: 'gives', 4764: 'layer', 4765: 'elocution', 4766: 'capuchin', 4767: 'cigarette', 4768: "nothin'", 4769: 'correct', 4770: 'close', 4771: 'joking', 4772: 'reliable', 4773: 'dad', 4774: 'kidney', 4775: 'gargoyle', 4776: 'world-class', 4777: 'guard', 4778: 'glum', 4779: 'stir', 4780: 'exhale', 4781: 'aisle', 4782: "queen's", 4783: 'exited', 4784: 'network', 4785: 'terrorizing', 4786: 'night-crawlers', 4787: 'novelty', 4788: 'go-near-', 4789: 'daughter', 4790: 'neil_gaiman:', 4791: 'forget', 4792: 'winnings', 4793: 'darn', 4794: 'result', 4795: 'peeved', 4796: 'sell', 4797: 'prize', 4798: 'bartholomã©:', 4799: 'hey', 4800: 'happens', 4801: "somethin's", 4802: 'presently', 4803: 'omigod', 4804: 'wally:', 4805: 'delays', 4806: "'pu", 4807: "y'see", 4808: 'sounded', 4809: 'squishee', 4810: '100', 4811: 'raises', 4812: 'tasimeter', 4813: 'crunch', 4814: 'stalin', 4815: 'carl_carlson:', 4816: 'spews', 4817: 'rhode', 4818: 'ride', 4819: 'lock', 4820: '3', 4821: 'doors', 4822: "eatin'", 4823: 'rods', 4824: 'patterns', 4825: 'moment', 4826: 'nascar', 4827: 'quotes', 4828: 'fires', 4829: 'yawns', 4830: 'dressing', 4831: 'chubby', 4832: 'hours', 4833: "puttin'", 4834: 'stats', 4835: 'equivalent', 4836: 'tom', 4837: 'him', 4838: 'profiling', 4839: 'diamond', 4840: 'mate', 4841: 'carl:', 4842: 'easy-going', 4843: 'chorus:', 4844: "crawlin'", 4845: "i'm", 4846: 'social', 4847: 'pushing', 4848: 'kickoff', 4849: 'streetcorner', 4850: 'refund', 4851: 'source', 4852: 'rid', 4853: 'haikus', 4854: 'runners', 4855: 'process', 4856: 'king', 4857: 'france', 4858: 'jerky', 4859: 'gasps', 4860: 'afloat', 4861: 'safely', 4862: "england's", 4863: 'pian-ee', 4864: 'rasputin', 4865: 'internet', 4866: 'nudge', 4867: 'oh-so-sophisticated', 4868: 'backbone', 4869: 'mortgage', 4870: 'mayan', 4871: 'person', 4872: 'ooh', 4873: "elmo's", 4874: 'awwww', 4875: 'referee', 4876: "rustlin'", 4877: 'releases', 4878: 'confused', 4879: "you've", 4880: 'wedding', 4881: 'kidnaps', 4882: 'loudly', 4883: 'restless', 4884: 'mic', 4885: 'dessert', 4886: 'delivery_man:', 4887: 'conversion', 4888: 'flourish', 4889: 'much', 4890: 'achem', 4891: 'fudd', 4892: 'code', 4893: 'dã¼ff', 4894: 'feminist', 4895: 'gr-aargh', 4896: 'using', 4897: 'raking', 4898: 'barely', 4899: 'tradition', 4900: 'shaggy', 4901: 'slap', 4902: 'thorn', 4903: 'scarf', 4904: 'drop-off', 4905: 'knocks', 4906: 'actor', 4907: 'uncreeped-out', 4908: 'sky', 4909: 'tubman', 4910: 'exception:', 4911: 'lanes', 4912: 'wagering', 4913: "i'll", 4914: 'skins', 4915: 'sprawl', 4916: '50%', 4917: 'that', 4918: 'dizer', 4919: 'forget-me-drinks', 4920: 'fan', 4921: 'refinanced', 4922: 'anger', 4923: 'mail', 4924: 'eighty-one', 4925: 'cushion', 4926: 'cyrano', 4927: 'gruff', 4928: 'starters', 4929: 'cocoa', 4930: 'everybody', 4931: 'brought', 4932: 'honored', 4933: 'lugs', 4934: 'sniffs', 4935: 'desperately', 4936: 'instantly', 4937: 'heart-broken', 4938: 'americans', 4939: 'squirrels', 4940: 'near', 4941: "murphy's", 4942: 'hostages', 4943: "year's", 4944: 'covers', 4945: 'decide', 4946: 'dateline:', 4947: 'churchill', 4948: "getting'", 4949: 'grateful', 4950: 'cursed', 4951: 'remembers', 4952: 'crowd:', 4953: 'massage', 4954: 'incredulous', 4955: 'ã', 4956: 'sweetie', 4957: 'appeals', 4958: 'microphone', 4959: 'again', 4960: 'unsanitary', 4961: 'bike', 4962: 'hostile', 4963: 'publishers', 4964: 'burp', 4965: 'heh-heh', 4966: 'driver', 4967: 'bought', 4968: 'so-called', 4969: 'groan', 4970: 'trash', 4971: "challengin'", 4972: 'oblongata', 4973: 'breakdown', 4974: 'honeys', 4975: 'david', 4976: 'clear', 4977: 'tall', 4978: 'malfeasance', 4979: 'dials', 4980: 'bar-boy', 4981: 'girl-bart', 4982: 'tee', 4983: 'beers', 4984: 'grocery', 4985: 'admitting', 4986: 'despite', 4987: 'fwooof', 4988: 'female_inspector:', 4989: 'renew', 4990: "beer's", 4991: 'captain', 4992: 'comeback', 4993: 'thirteen', 4994: 'capitol', 4995: 'lemonade', 4996: 'presidential', 4997: 'conversation', 4998: 'apulina', 4999: 'thrown', 5000: 'intriguing', 5001: 'maher', 5002: 'means', 5003: 'germany', 5004: 'lady_duff:', 5005: 'regulars', 5006: 'revenge', 5007: 'flips', 5008: 'heatherton', 5009: 'grubby', 5010: 'moron', 5011: 'sorts', 5012: 'indeed', 5013: "cleanin'", 5014: 'sense', 5015: 'commit', 5016: 'normal', 5017: 'al_gore:', 5018: 'priority', 5019: 'y', 5020: 'mortal', 5021: 'steamed', 5022: 'swine', 5023: 'geysir', 5024: 'drink', 5025: 'awkward', 5026: 'flea:', 5027: 'glitz', 5028: 'cocktail', 5029: 'top', 5030: 'else', 5031: "grandmother's", 5032: 'disgusted', 5033: 'points', 5034: 'plastic', 5035: 'kodos:', 5036: 'lainie:', 5037: 'rage', 5038: 'packets', 5039: 'lewis', 5040: 'wanna', 5041: 'ebullient', 5042: 'parents', 5043: 'madison', 5044: 'laney_fontaine:', 5045: "marge's", 5046: 'germs', 5047: 'lucius', 5048: 'whoopi', 5049: 'amends', 5050: 'fill', 5051: 'boozer', 5052: "game's", 5053: 'generosity', 5054: 'calculate', 5055: 'punches', 5056: 'chunk', 5057: 'champs', 5058: 'cheers', 5059: 'dancing', 5060: 'falsetto', 5061: 'six', 5062: 'castle', 5063: "linin'", 5064: 'be', 5065: 'jack_larson:', 5066: 'culkin', 5067: 'loathe', 5068: 'clears', 5069: '1979', 5070: 'whispered', 5071: 'insensitive', 5072: 'james', 5073: 'prizefighters', 5074: 'frink', 5075: 'lend', 5076: 'overturned', 5077: 'pile', 5078: 'trapping', 5079: 'bunch', 5080: 'fortensky', 5081: 'arab_man:', 5082: 'sooner', 5083: 'passes', 5084: 'eye', 5085: 'marquee', 5086: 'idiot', 5087: 'pledge', 5088: 'u', 5089: 'admiring', 5090: 'naegle', 5091: 'nigeria', 5092: 'jaegermeister', 5093: 'charges', 5094: 'malted', 5095: 'chow', 5096: 'gil_gunderson:', 5097: 'assert', 5098: 'possessions', 5099: 'pond', 5100: 'pleased', 5101: 'sleeps', 5102: 'negative', 5103: 'understand', 5104: "squeezin'", 5105: 'wasting', 5106: 'address', 5107: 'schemes', 5108: 'linda_ronstadt:', 5109: 'flash-fry', 5110: 'waist', 5111: 'waylon_smithers:', 5112: 'awkwardly', 5113: 'neighborhood', 5114: 'radio', 5115: 'kadlubowski', 5116: 'celebrities', 5117: 'whoo', 5118: 'cost', 5119: 'heather', 5120: 'disturbing', 5121: 'citizens', 5122: 'noooooooooo', 5123: 'joint', 5124: 'middle', 5125: 'whoever', 5126: 'changes', 5127: 'rafter', 5128: "c'mere", 5129: 'perã³n', 5130: 'disgraceful', 5131: 'crisis', 5132: 'land', 5133: 'thoughts', 5134: 'grey', 5135: 'zone', 5136: 'mccall', 5137: 'imported-sounding', 5138: 'become', 5139: 'hang', 5140: 'pointy', 5141: 'hose', 5142: 'burns', 5143: 'ruby-studded', 5144: 'fine', 5145: 'left', 5146: 'emphasis', 5147: 'denser', 5148: 'followed', 5149: 'good-looking', 5150: 'male_singers:', 5151: 'grampa_simpson:', 5152: 'nail', 5153: 'arise', 5154: 'night', 5155: 'young_homer:', 5156: 'into', 5157: 'hunky', 5158: 'twelve', 5159: 'rock', 5160: 'accusing', 5161: "jimbo's_dad:", 5162: 'rumaki', 5163: 'boo', 5164: 'idioms', 5165: 'easily', 5166: 'soap', 5167: 'diving', 5168: 'sharing', 5169: 'appointment', 5170: 'family-owned', 5171: 'experience', 5172: 'evening', 5173: 'thomas', 5174: 'site', 5175: 'faint', 5176: 'destroyed', 5177: 'smells', 5178: "tinklin'", 5179: 'alpha-crow', 5180: 'religious', 5181: 'misconstrue', 5182: 'tolerable', 5183: 'sold', 5184: 'drawing', 5185: 'whaddya', 5186: 'natured', 5187: 'pictured', 5188: 'associate', 5189: 'suppose', 5190: 'bees', 5191: 'cupid', 5192: 'burg', 5193: 'conclusions', 5194: 'surprised/thrilled', 5195: 'sketch', 5196: 'worried', 5197: 'four', 5198: 'girls', 5199: 'breathtaking', 5200: 'elect', 5201: 'man', 5202: 'monday', 5203: 'liquor', 5204: 'chinese', 5205: 'refiero', 5206: 'barbara', 5207: 'bedridden', 5208: 'website', 5209: 'edner', 5210: "renee's", 5211: 'best', 5212: 'remembering', 5213: 'brine', 5214: 'augustus', 5215: 'effervescence', 5216: 'she', 5217: 'heartily', 5218: 'casual', 5219: 'sisters', 5220: 'attention', 5221: 'at', 5222: 'unison', 5223: 'detail', 5224: 'amused', 5225: 'exploiter', 5226: 'ha', 5227: 'fat', 5228: 'broken:', 5229: "takin'", 5230: 'marjorie', 5231: 'skinner', 5232: 'obese', 5233: 'carefully', 5234: 'adeleine', 5235: 'sheepish', 5236: 'short', 5237: 'crew', 5238: 'glorious', 5239: 'glitterati', 5240: 'pointless', 5241: 'pages', 5242: 'bring', 5243: 'rolled', 5244: 'waste', 5245: 'abusive', 5246: 'dunno', 5247: "we'll", 5248: 'jay', 5249: 'hall', 5250: 'exciting', 5251: 'smart', 5252: 'stinky', 5253: 'cigarettes', 5254: 'tasty', 5255: 'machine', 5256: 'mind-numbing', 5257: 'justice', 5258: 'john', 5259: 'dint', 5260: 'renovations', 5261: 'slight', 5262: "table's", 5263: "ain't", 5264: 'porn', 5265: 'certain', 5266: 'cards', 5267: 'minimum', 5268: 'hare-brained', 5269: 'boxer', 5270: 'spamming', 5271: 'aer', 5272: 'fears', 5273: 'sabermetrics', 5274: 'farthest', 5275: 'gesture', 5276: 'oddest', 5277: 'smoothly', 5278: 'mouth', 5279: 'besides', 5280: 'wheel', 5281: 'nfl_narrator:', 5282: 'flanders:', 5283: 'senators', 5284: 'disgracefully', 5285: 'once', 5286: 'hoping', 5287: 'raise', 5288: 'biggest', 5289: 'ticket', 5290: 'combine', 5291: 'character', 5292: 'manjula_nahasapeemapetilon:', 5293: 'brightening', 5294: 'strangles', 5295: 'kissingher', 5296: 'blues', 5297: 'dude', 5298: 'somehow', 5299: "bar's", 5300: 'blubberino', 5301: 'misfire', 5302: 'enthusiasm', 5303: 'ashtray', 5304: 'x', 5305: 'tell', 5306: 'heartless', 5307: 'aristotle:', 5308: 'cheese', 5309: 'stamps', 5310: 'son', 5311: 'phony', 5312: 'teams', 5313: "b-52's:", 5314: 'balloon', 5315: 'fans', 5316: 'homer_', 5317: 'stores', 5318: 'superior', 5319: 'disappointing', 5320: 'plane', 5321: 'fury', 5322: 'christopher', 5323: 'lawyer', 5324: 'limits', 5325: 'pickle', 5326: 'rubbed', 5327: 'detective', 5328: 'puke-holes', 5329: 'more', 5330: 'jubilant', 5331: 'minister', 5332: 'gunter:', 5333: 'gas', 5334: 'amber_dempsey:', 5335: 'moe-clone', 5336: 'impress', 5337: 'sixty', 5338: '1973', 5339: 'completely', 5340: 'touch', 5341: 'bonfire', 5342: 'arrange', 5343: 'artie', 5344: 'friday', 5345: 'pink', 5346: 'plan', 5347: 'blamed', 5348: 'cash', 5349: 'parked', 5350: 'politics', 5351: 'winston', 5352: 'startled', 5353: 'offended', 5354: 'brings', 5355: 'chinua', 5356: 'thankful', 5357: 'yes', 5358: 'sacrilicious', 5359: 'hat', 5360: 'wang', 5361: 'ear', 5362: 'cut', 5363: "drinkin'", 5364: 'ing', 5365: 'koholic', 5366: 'self-esteem', 5367: 'yell', 5368: 'terror', 5369: 'all', 5370: 'innocuous', 5371: 'tender', 5372: 'literature', 5373: 'gunk', 5374: 'tracks', 5375: 'patrons', 5376: 'crossed', 5377: 'down', 5378: 'idiots', 5379: 'drinks', 5380: 'hawaii', 5381: 'tar-paper', 5382: 'curse', 5383: 'town', 5384: 'delighted', 5385: 'dismissive', 5386: 'dreamed', 5387: 'times', 5388: 'save', 5389: 'pregnancy', 5390: 'bold', 5391: 'hearing', 5392: 'move', 5393: 'journey', 5394: 'slow', 5395: "who'll", 5396: 'lisa', 5397: 'stretch', 5398: 'shard', 5399: 'beverage', 5400: 'it', 5401: 'rich', 5402: 'eurotrash', 5403: 'cherry', 5404: 'zinged', 5405: 'cheaper', 5406: 'pugilist', 5407: 'ad', 5408: 'w-a-3-q-i-zed', 5409: 'dã¼ffenbraus', 5410: 'ah-ha', 5411: 'law', 5412: 'showed', 5413: 'worth', 5414: 'declan_desmond:', 5415: 'navy', 5416: 'mel', 5417: 'shesh', 5418: "springfield's", 5419: 'immiggants', 5420: 'names', 5421: 'victim', 5422: "who'da", 5423: 'though:', 5424: '50-60', 5425: 'rugged', 5426: 'careful', 5427: 'nectar', 5428: 'lou', 5429: 'bloodiest', 5430: 'trust', 5431: 'what', 5432: 'huh', 5433: 'labor', 5434: 'der', 5435: 'needy', 5436: 'fraud', 5437: 'railroad', 5438: 'walther_hotenhoffer:', 5439: 'huggenkiss', 5440: 'wipe', 5441: "should've", 5442: 'micronesian', 5443: 'grim', 5444: 'whether', 5445: 'blinds', 5446: 'fdic', 5447: 'appearance-altering', 5448: "drivin'", 5449: 'put', 5450: 'starving', 5451: 'impatient', 5452: "'tis", 5453: 'plastered', 5454: 'wooooo', 5455: 'dryer', 5456: 'motto', 5457: 'start', 5458: 'nantucket', 5459: 'swimming', 5460: 'richard:', 5461: 'beached', 5462: 'maybe', 5463: 'is:', 5464: 'dishrag', 5465: 'boozy', 5466: 'studied', 5467: 'tummies', 5468: 'patty_bouvier:', 5469: 'hooky', 5470: 'blood-thirsty', 5471: 'same', 5472: 'view', 5473: 'ab', 5474: 'albert', 5475: 'hates', 5476: 'morning', 5477: 'experiments', 5478: 'ruined', 5479: 'kissing', 5480: 'handshake', 5481: 'learned', 5482: 'turning', 5483: 'failure', 5484: 'fanciest', 5485: 'guessing', 5486: 'sap', 5487: 'muslim', 5488: 'dumbass', 5489: 'shame', 5490: 'this', 5491: 'tapestry', 5492: 'donuts', 5493: 'dull', 5494: 'fondly', 5495: 'heads', 5496: 'stole', 5497: 'ohh', 5498: 'caveman', 5499: 'scruffy_blogger:', 5500: 'laney', 5501: 'unattractive', 5502: 'hide', 5503: 'i/you', 5504: 'habitrail', 5505: 'tomato', 5506: 'cajun', 5507: 'quick', 5508: 'prank', 5509: 'cueball', 5510: 'bill', 5511: 'tsking', 5512: 'vacuum', 5513: 'aw', 5514: 'electronic', 5515: 'ingredient', 5516: 'swishkabobs', 5517: "bringin'", 5518: 'terrace', 5519: 'california', 5520: 'gus', 5521: 'wild', 5522: 'appear', 5523: 'rub-a-dub', 5524: 'mural', 5525: 'across', 5526: 'lurleen_lumpkin:', 5527: 'pizzicato', 5528: 'grub', 5529: 'eyeing', 5530: 'mushy', 5531: 'jump', 5532: 'tv_daughter:', 5533: 'wh', 5534: 'when', 5535: 'cares', 5536: 'tax', 5537: 'moan', 5538: 'shrieks', 5539: 'geez', 5540: 'laid', 5541: 'agent', 5542: 'george', 5543: 'b', 5544: 'orifice', 5545: 'beyond', 5546: 'temples', 5547: 'hateful', 5548: 'sitcom', 5549: 'nose', 5550: 'crack', 5551: 'draw', 5552: 'junkyard', 5553: 'watered', 5554: 'dames', 5555: 'jar', 5556: 'popped', 5557: 'toxins', 5558: 'broom', 5559: 'all:', 5560: 'ducked', 5561: 'sunny', 5562: 'pantry', 5563: 'schorr', 5564: "that'll", 5565: 'brunch', 5566: 'published', 5567: 'sea', 5568: 'whale', 5569: 'treat', 5570: 'serve', 5571: 'burger', 5572: 'annus', 5573: 'expect', 5574: 'chill', 5575: 'fica', 5576: 'adjourned', 5577: 'looked', 5578: 'dry', 5579: 'spiritual', 5580: 'wok', 5581: 'diaper', 5582: 'country', 5583: 'gees', 5584: 'fistiana', 5585: 'hideous', 5586: "what'd", 5587: 'rafters', 5588: 'othello', 5589: 'tenor:', 5590: 'separator', 5591: 'wieners', 5592: 'stingy', 5593: 'dazed', 5594: 'suddenly', 5595: 'desire', 5596: 'based', 5597: 'compare', 5598: 'statues', 5599: 'head-gunk', 5600: 'mess', 5601: 'bleeding', 5602: 'fainted', 5603: 'braun:', 5604: 'hangout', 5605: 'choking', 5606: 'rainforest', 5607: 'shrugs', 5608: 'fritz', 5609: 'feld', 5610: 'utensils', 5611: 'jam', 5612: 'gum', 5613: 'doll-baby', 5614: "homer's_brain:", 5615: 'babe', 5616: 'little_man:', 5617: 'imagine', 5618: 'emporium', 5619: 'inflated', 5620: 'alive', 5621: 'practically', 5622: 'padre', 5623: 'jacks', 5624: 'spellbinding', 5625: 'in-ground', 5626: 'decide:', 5627: 'buttocks', 5628: 'sympathetic', 5629: 'jewish', 5630: 'place', 5631: 'full-time', 5632: 'excellent', 5633: 'raging', 5634: 'listening', 5635: 'attempting', 5636: '||exclamation_mark||', 5637: 'gutenberg', 5638: 'fonzie', 5639: 'laramie', 5640: 'pepper', 5641: 'gimme', 5642: 'writer:', 5643: 'snake_jailbird:', 5644: 'whiny', 5645: 'spied', 5646: 'donated', 5647: 'market', 5648: 'protesting', 5649: 'stealings', 5650: "round's", 5651: 'bart_simpson:', 5652: 'does', 5653: 'exhibit', 5654: 'calls', 5655: 'plaintive', 5656: 'ago', 5657: 'nuclear', 5658: 'ominous', 5659: 'telemarketing', 5660: 'intervention', 5661: 'pig', 5662: 'cartoons', 5663: 'sponsoring', 5664: 'room', 5665: 'spot', 5666: 'vermont', 5667: 'around', 5668: 'triumphantly', 5669: 'venom', 5670: 'window', 5671: 'enabling', 5672: 'depending', 5673: 'class', 5674: 'beer-dorf', 5675: 'ticks', 5676: 'make:', 5677: 'past', 5678: 'curiosity', 5679: 'jay:', 5680: 'dynamite', 5681: "mo'", 5682: 'meaningfully', 5683: 'match', 5684: 'sign', 5685: 'solved', 5686: 'wondered', 5687: 'kick', 5688: 'baby', 5689: 'larry', 5690: 'washed', 5691: 'sideshow', 5692: 'brace', 5693: 'youse', 5694: 'judgments', 5695: 'thirty', 5696: 'cheesecake', 5697: 'ton', 5698: 'lousy', 5699: 'red', 5700: 'friendly', 5701: 'impeach', 5702: 'alarm', 5703: 'mistake', 5704: 'healthier', 5705: 'hats', 5706: '||new_line||', 5707: 'peanut', 5708: 'jacques', 5709: 'realized', 5710: 'mm-hmm', 5711: 'selma', 5712: 'be-stainã¨d', 5713: 'connection', 5714: 'remorseful', 5715: 'beady', 5716: 'newsies', 5717: 'fancy', 5718: 'no', 5719: "'kay-zugg'", 5720: 'forty', 5721: 'leno', 5722: 'warned', 5723: 'kinderhook', 5724: 'singers:', 5725: 'bubbles-in-my-nose-y', 5726: 'hop', 5727: 'workers', 5728: 'supreme', 5729: 'grammys', 5730: 'lecture', 5731: 'wage', 5732: "knockin'", 5733: 'trolls', 5734: 'my-y-y-y-y-y', 5735: 'wraps', 5736: 'johnny', 5737: 'secret', 5738: 'macaulay', 5739: 'groans', 5740: 'understood', 5741: 'organ', 5742: 'brother', 5743: 'avenue', 5744: 'patrons:', 5745: 'century', 5746: 'eddie:', 5747: 'something:', 5748: 'pint', 5749: 'reed', 5750: 'intoxicants', 5751: "town's", 5752: 'loan', 5753: 'brain-switching', 5754: "she'll", 5755: 'nigel_bakerbutcher:', 5756: 'yep', 5757: 'getcha', 5758: 'belt', 5759: 'u2:', 5760: 'letter', 5761: 'stranger:', 5762: 'gin', 5763: 'cobbling', 5764: 'birthday', 5765: 'sec_agent_#2:', 5766: 'crow', 5767: 'prison', 5768: 'relative', 5769: 'without', 5770: 'stops', 5771: 'sub-monkeys', 5772: 'choices', 5773: 'sustain', 5774: 'drag', 5775: 'regretted', 5776: 'getup', 5777: 'pockets', 5778: 'jane', 5779: 'derisive', 5780: 'low', 5781: 'beans', 5782: 'shag', 5783: 'prepared', 5784: 'notably', 5785: 'girl', 5786: 'torn', 5787: 'bathed', 5788: 'rude', 5789: 'summer', 5790: 'plant', 5791: 'giant', 5792: 'recommend', 5793: 'edna', 5794: 'information', 5795: "bein'", 5796: 'wisconsin', 5797: 'fuzzlepitch', 5798: 'snake', 5799: 'forty-two', 5800: 'counter', 5801: 'derek', 5802: 'ringing', 5803: 'mixed', 5804: 'everyone', 5805: 'boozehound', 5806: 'days', 5807: 'rims', 5808: 'newsletter', 5809: 'pumping', 5810: 'miss_lois_pennycandy:', 5811: 'jernt', 5812: 'professor', 5813: 'sneaky', 5814: 'sells', 5815: 'turned', 5816: 'option', 5817: 'statue', 5818: 'vote', 5819: "neighbor's", 5820: 'cleaning', 5821: 'slick', 5822: 'beginning', 5823: 'africanized', 5824: "when's", 5825: "smokin'", 5826: 'ahhhh', 5827: 'unforgettable', 5828: 'killer', 5829: 'david_byrne:', 5830: 'while', 5831: 'dank', 5832: 'stopped', 5833: 'happily', 5834: 'naturally', 5835: 'settlement', 5836: 'verdict', 5837: 'apology', 5838: 'goal', 5839: 'gifts', 5840: 'venture', 5841: 'sticking', 5842: "foolin'", 5843: 'kent', 5844: 'scratcher', 5845: 'helps', 5846: 'booze', 5847: 'sauce', 5848: 'moonnnnnnnn', 5849: 'togetherness', 5850: 'lovejoy', 5851: '1-800-555-hugs', 5852: 'slurred', 5853: 'hardhat', 5854: 'life:', 5855: 'repressed', 5856: 'cheapskates', 5857: 'puffy', 5858: 'following', 5859: 'chair', 5860: 'labels', 5861: "showin'", 5862: 'squeezed', 5863: 'says', 5864: 'shaken', 5865: 'inning', 5866: 'boneheaded', 5867: 'compressions', 5868: 'register', 5869: 'village', 5870: 'graves', 5871: 'crapmore', 5872: 'affection', 5873: 'seen', 5874: 'noise', 5875: '2nd_voice_on_transmitter:', 5876: 'knock', 5877: 'mimes', 5878: 'wish', 5879: 'stinger', 5880: 'badmouth', 5881: 'au', 5882: 'thesaurus', 5883: 'einstein', 5884: 'mostrar', 5885: 'stalwart', 5886: 'bret', 5887: 'thru', 5888: '/', 5889: 'cross-country', 5890: 'winch', 5891: 'won', 5892: 'hobo', 5893: 'commission', 5894: 'drawn', 5895: 'listened', 5896: 'shot', 5897: 'checks', 5898: 'all-american', 5899: 'ehhhhhhhh', 5900: 'blossoming', 5901: 'direction', 5902: 'errrrrrr', 5903: 'valley', 5904: 'pays', 5905: 'inexorable', 5906: 'involving', 5907: 'infestation', 5908: 'statistician', 5909: 'dum-dum', 5910: 'kneeling', 5911: 'rivalry', 5912: 'actress', 5913: 'barn', 5914: 'sympathizer', 5915: "sittin'", 5916: 'hunter', 5917: 'will', 5918: "livin'", 5919: 'morning-after', 5920: 'roomy', 5921: 'lumpa', 5922: 'human', 5923: 'dealt', 5924: 'royal', 5925: 'sets', 5926: 'foundation', 5927: 'finale', 5928: 'sweetest', 5929: 'almost', 5930: 'suburban', 5931: 'themselves', 5932: 'fire_inspector:', 5933: 'democracy', 5934: 'mobile', 5935: 'attack', 5936: 'bums', 5937: 'talking', 5938: 'himself', 5939: 'picky', 5940: 'log', 5941: 'personal', 5942: 'badge', 5943: 'straighten', 5944: 'bender:', 5945: 'jolly', 5946: 'well', 5947: 'elves:', 5948: 'stein-stengel-', 5949: 'fell', 5950: 'snout', 5951: 'crime', 5952: 'brockelstein', 5953: 'conditioner', 5954: 'written', 5955: 'bam', 5956: 'barney-shaped_form:', 5957: 'ignorance', 5958: "'evening", 5959: 'usual', 5960: 'see', 5961: 'duffed', 5962: 'shades', 5963: 'tight', 5964: 'vincent', 5965: 'recipe', 5966: 'jerking', 5967: 'pity', 5968: 'confession', 5969: 'blobbo', 5970: 'spinning', 5971: 'tablecloth', 5972: 'scene', 5973: 'refreshing', 5974: 'addiction', 5975: 'complaint', 5976: 'better', 5977: 'ye', 5978: 'meaningless', 5979: 'hafta', 5980: "raggin'", 5981: 'future', 5982: 'stands', 5983: 'twice', 5984: 'chuck', 5985: 'inspiring', 5986: 'clandestine', 5987: 'blue', 5988: 'spoon', 5989: 'resigned', 5990: 'use', 5991: 'driving', 5992: 'murmur', 5993: 'yak', 5994: 'gave', 5995: 'cowboy', 5996: 'usually', 5997: 'gasp', 5998: "what's", 5999: 'lotta', 6000: "poundin'", 6001: 'ons', 6002: 'sadly', 6003: 'strongly', 6004: 'cuff', 6005: 'illegally', 6006: 'pay', 6007: 'product', 6008: "city's", 6009: 'proposition', 6010: 'meant', 6011: 'closing', 6012: 'adventure', 6013: 'smoke', 6014: 'swelling', 6015: 'natural', 6016: 'unusually', 6017: 'muttering', 6018: 'bumpy-like', 6019: 'carve', 6020: 'repeating', 6021: '_powers:', 6022: 'hushed', 6023: 'dilemma', 6024: 'advertise', 6025: 'white_rabbit:', 6026: 'agent_miller:', 6027: 'brown', 6028: 'faded', 6029: 'shelf', 6030: 'sixty-nine', 6031: 'mansions', 6032: 'bigger', 6033: 'joke', 6034: 'gay', 6035: 'play/', 6036: 'knuckle-dragging', 6037: "watchin'", 6038: 'fake', 6039: 'round', 6040: 'karaoke', 6041: 'amid', 6042: 'harmony', 6043: 'beat', 6044: 'attractive', 6045: 'shows', 6046: 'limber', 6047: 'emergency', 6048: 'gator:', 6049: 'cough', 6050: 'disgrace', 6051: 'frogs', 6052: 'mexicans', 6053: 'fingers', 6054: 'extremely', 6055: 'tha', 6056: 'sings', 6057: 'reached', 6058: 'publish', 6059: 'dean', 6060: 'sips', 6061: 'voicemail', 6062: 'speaking', 6063: "fishin'", 6064: 'imitating', 6065: 'eighty-three', 6066: 'owns', 6067: 'dumbbell', 6068: "'ceptin'", 6069: 'barf', 6070: 'director:', 6071: 'taught', 6072: 'sweden', 6073: 'about', 6074: 'health_inspector:', 6075: 'lie', 6076: 'pantsless', 6077: "writin'", 6078: 'dea-d-d-dead', 6079: 'loaded', 6080: 'grandã©', 6081: 'clench', 6082: 'county', 6083: 'contemptuous', 6084: 'ladder', 6085: 'hootie', 6086: 'cries', 6087: 'teacup', 6088: 'noggin', 6089: 'moe-ron', 6090: 'h', 6091: 'annual', 6092: "wife's", 6093: 'tire', 6094: 'solo', 6095: 'junebug', 6096: 'chicks', 6097: 'work', 6098: 'dads', 6099: 'newsweek', 6100: 'nods', 6101: 'bidet', 6102: 'beating', 6103: 'pickled', 6104: 'ninety-seven', 6105: 'sucks', 6106: 'crayola', 6107: 'cat', 6108: 'twins', 6109: 'chum', 6110: 'fault', 6111: 'urge', 6112: 'ideas', 6113: 'infiltrate', 6114: 'dawning', 6115: 'displeased', 6116: 'speed', 6117: 'huddle', 6118: 'absolutely', 6119: 'sing', 6120: 'neanderthal', 6121: 'mason', 6122: "hasn't", 6123: 'mmm', 6124: 'buddy', 6125: 'reptile', 6126: 'wounds', 6127: 'gary:', 6128: 'henry', 6129: 'silent', 6130: 'smelly', 6131: 'open-casket', 6132: 'thanksgiving', 6133: 'button', 6134: 'rueful', 6135: 'italian', 6136: 'measurements', 6137: 'life-sized', 6138: 'polygon', 6139: 'pleasure', 6140: 'barflies', 6141: 'mumble', 6142: 'tv_husband:', 6143: 'mechanical', 6144: 'alternative', 6145: 'pip', 6146: 'catch-phrase', 6147: 'sangre', 6148: 'parenting', 6149: 'ohmygod', 6150: "stayin'", 6151: 'courts', 6152: "wearin'", 6153: 'dislike', 6154: 'cell-ee', 6155: 'care', 6156: 'sudoku', 6157: 'turn', 6158: 'catholic', 6159: 'woo-hoo', 6160: 'wayne:', 6161: 'books', 6162: 'pageant', 6163: 'possibly', 6164: 'ought', 6165: 'catch', 6166: 'series', 6167: 'ease', 6168: 'puff', 6169: 'bucket', 6170: 'ã€', 6171: 'dinks', 6172: 'little_hibbert_girl:', 6173: 'relax', 6174: 'schizophrenia', 6175: 'gulps', 6176: 'sight', 6177: 'canyoner-oooo', 6178: 'stickers', 6179: 'government', 6180: 'starting', 6181: 'kazoo', 6182: 'consoling', 6183: 'heart', 6184: 'pop', 6185: 'accepting', 6186: 'lenny_leonard:', 6187: 'school', 6188: 'stevie', 6189: 'combines', 6190: 'hotenhoffer', 6191: 'dracula', 6192: 'nuked', 6193: 'mirthless', 6194: 'blessing', 6195: 'belch', 6196: 'sneering', 6197: "dog's", 6198: 'terrified', 6199: 'mike', 6200: 'fly', 6201: 'gamble', 6202: 'conspiratorial', 6203: 'loves', 6204: 'warmth', 6205: 'lump', 6206: 'sugar-free', 6207: 'doubt', 6208: 'customers', 6209: 'scum-sucking', 6210: 'twelveball', 6211: 'steel', 6212: 'expecting', 6213: 'ring', 6214: 'julienne', 6215: 'cover', 6216: 'pep', 6217: 'expose', 6218: 'snotball', 6219: 'because', 6220: 'transmission', 6221: 'settles', 6222: 'pitcher', 6223: 'wife', 6224: 'microbrew', 6225: 'protesters', 6226: 'bread', 6227: 'copy', 6228: 'driveability', 6229: 'rapidly', 6230: 'i', 6231: 'brothers', 6232: 'notch', 6233: 'stares', 6234: 'massachusetts', 6235: 'defeated', 6236: 'monroe', 6237: 'plenty', 6238: 'tang', 6239: 'ahead', 6240: 'harvesting', 6241: 'mustard', 6242: 'funniest', 6243: 'doreen:', 6244: 'being', 6245: 'clothespins:', 6246: 'locklear', 6247: "cuckold's", 6248: 'tense', 6249: 'warning', 6250: 'theatrical', 6251: 'private', 6252: 'andalay', 6253: 'religion', 6254: 'visas', 6255: 'listens', 6256: 'wolveriskey', 6257: 'sobs', 6258: 'warm_female_voice:', 6259: 'allowance', 6260: 'melodramatic', 6261: 'hate', 6262: 'mt', 6263: 'in-in-in', 6264: 'ginger', 6265: 'happy', 6266: 'stagy', 6267: 'acquaintance', 6268: 'remote', 6269: 'stengel', 6270: 'terrifying', 6271: 'helping', 6272: 'busted', 6273: 'pee', 6274: 'releasing', 6275: 'faces', 6276: 'two-thirds-empty', 6277: 'loyal', 6278: 'billy_the_kid:', 6279: 'scores', 6280: 'deli', 6281: 'sassy', 6282: 'pissed', 6283: "santa's", 6284: 'necessary', 6285: 'able', 6286: 'cable', 6287: "bettin'", 6288: 'prohibit', 6289: "there's", 6290: 'cleaned', 6291: 'item', 6292: 'fox', 6293: 'brooklyn', 6294: 'trunk', 6295: 'screams', 6296: 'lurleen', 6297: 'celeste', 6298: 'helpful', 6299: 'sensitivity', 6300: 'ze-ro', 6301: 'shhh', 6302: 'march', 6303: 'ura', 6304: 'disappointed', 6305: 'neon', 6306: 'totalitarians', 6307: 'happen', 6308: 'raggie', 6309: 'coms', 6310: 'heaving', 6311: 'presents', 6312: 'knowingly', 6313: 'cheery', 6314: 'yellow-belly', 6315: 'a', 6316: 'entering', 6317: 'sweater', 6318: 'sheets', 6319: 'less', 6320: 'lachrymose', 6321: 'lis', 6322: 'fact', 6323: 'snatch', 6324: 'thing:', 6325: 'naked', 6326: 'problemo', 6327: 'pretends', 6328: "buyin'", 6329: 'donate', 6330: 'ribbon', 6331: 'died', 6332: 'beards', 6333: 'gag', 6334: 'five', 6335: 'design', 6336: 'sugar-me-do', 6337: 'annoyed', 6338: 'conversations', 6339: 'traffic', 6340: 'chest', 6341: "won't", 6342: 'darts', 6343: 'said:', 6344: 'cow', 6345: "i-i'm", 6346: 'methinks', 6347: 'ivanna', 6348: 'gang', 6349: 'jukebox', 6350: 'wha', 6351: 'disguise', 6352: "blowin'", 6353: 'bowl', 6354: 'nasa', 6355: 'acceptance', 6356: 'hexa-', 6357: 'appendectomy', 6358: 'freed', 6359: 'shill', 6360: 'father', 6361: 'release', 6362: 'fry', 6363: 'exact', 6364: 'har', 6365: 'voodoo', 6366: 'designated', 6367: 'pfft', 6368: 'also', 6369: 'sidekick', 6370: 'awfully', 6371: 'correcting', 6372: "kearney's_dad:", 6373: 'discuss', 6374: 'nightmares', 6375: 'cleveland', 6376: 'woman:', 6377: "that's", 6378: 'name', 6379: 'price', 6380: 'fayed', 6381: 'ballot', 6382: 'fork', 6383: 'edna-lover-one-seventy-two', 6384: 'chosen', 6385: 'marmaduke', 6386: 'birthplace', 6387: 'shaky', 6388: 'caper', 6389: 'haw', 6390: 'bursts', 6391: 'jeez', 6392: 'tuna', 6393: 'hears', 6394: 'perverse', 6395: 'sir', 6396: 'slugger', 6397: 'burning', 6398: 'storms', 6399: 'blind', 6400: "wonderin'", 6401: 'lingus', 6402: 'contact', 6403: 'sissy', 6404: 'sweetheart', 6405: 'grumpy', 6406: 'sanitary', 6407: 'kid', 6408: 'aged_moe:', 6409: 'charlie', 6410: 'insured', 6411: 'monster', 6412: 'bashir', 6413: 'bolting', 6414: 'after', 6415: 'hygienically', 6416: 'rubs', 6417: 'ohhhh', 6418: 'coupon', 6419: 'hm', 6420: 'mind', 6421: "cashin'", 6422: 'blown', 6423: 'steaming', 6424: 'guts', 6425: 'oopsie', 6426: 'second', 6427: 'troy:', 6428: 'reward', 6429: 'long', 6430: 'du', 6431: 'belly', 6432: 'safer', 6433: 'boisterous', 6434: 'libraries', 6435: 'not', 6436: 'gotcha', 6437: 'placed', 6438: 'well-wisher', 6439: 'couple', 6440: 'complete', 6441: 'missing', 6442: 'arrived', 6443: 'assistant', 6444: 'asking', 6445: 'rewound', 6446: 'booth', 6447: "cont'd:", 6448: 'annoying', 6449: 'rented', 6450: 'f-l-a-n-r-d-s', 6451: 'peaked', 6452: 'hearts', 6453: 'telegraph', 6454: 'cool', 6455: 'eight', 6456: 'seems', 6457: 'gimmick', 6458: 'wenceslas', 6459: 'reaches', 6460: "drawin'", 6461: 'orders', 6462: 'moe-near-now', 6463: 'prolonged', 6464: "washin'", 6465: '/mr', 6466: 'their', 6467: 'badly', 6468: 'whatcha', 6469: 'calendars', 6470: 'bottom', 6471: 'ronstadt', 6472: 'date', 6473: 'buddha', 6474: 'support', 6475: 'oooo', 6476: 'soothing', 6477: 'strap', 6478: 'cloudy', 6479: 'boxer:', 6480: 'flatly', 6481: 'wrestle', 6482: "he'd", 6483: 'got', 6484: 'al', 6485: 'dreary', 6486: 'miserable', 6487: 'shush', 6488: 'donut', 6489: 'whirlybird', 6490: 'underpants', 6491: 'beanbag', 6492: 'louse', 6493: 'causes', 6494: 'splendid', 6495: 'orgasmville', 6496: 'astronauts', 6497: 'wholeheartedly', 6498: 'co-sign', 6499: 'fish', 6500: 'shark', 6501: 'distaste', 6502: 'access', 6503: 'turlet', 6504: "hangin'", 6505: 'saying', 6506: 'cozy', 6507: 'favorite', 6508: 'jack', 6509: 'yesterday', 6510: 'honest', 6511: 'lenny', 6512: 'cummerbund', 6513: 'gentleman:', 6514: 'empty', 6515: 'glass', 6516: 'fondest', 6517: 'deeper', 6518: 'tying', 6519: 'farewell', 6520: 'informant', 6521: 'fl', 6522: 'decent', 6523: 'sagacity', 6524: 'played', 6525: 'sure', 6526: 'trick', 6527: 'brockman', 6528: 'robbers', 6529: 'english', 6530: 'record', 6531: "can't", 6532: 'hoagie', 6533: 'unhappy', 6534: 'nucular', 6535: 'upset', 6536: 'starts', 6537: 'lessons', 6538: 'knives', 6539: 'life', 6540: 'lager', 6541: "what'sa", 6542: 'some', 6543: 'wonderful', 6544: 'hooch', 6545: 'disappear', 6546: 'led', 6547: 'beard', 6548: 'fast', 6549: 'takes', 6550: 'fringe', 6551: 'nemo', 6552: 'question', 6553: 'deadly', 6554: 'i-i-i', 6555: 'pitch', 6556: 'sent', 6557: 'whee', 6558: 'invite', 6559: 'andy', 6560: "liberty's", 6561: 'glad', 6562: 'lurks', 6563: 'mulder', 6564: 'determined', 6565: 'schmoe', 6566: 'super-genius', 6567: 'rocks', 6568: 'sloppy', 6569: 'volunteer', 6570: "lookin'", 6571: 'nooo', 6572: 'audience', 6573: 'cop', 6574: 'sincerely', 6575: 'over', 6576: 'pulls', 6577: 'value', 6578: 'frink-y', 6579: 'lucinda', 6580: 'uh-oh', 6581: "smokin'_joe_frazier:", 6582: 'throats', 6583: 'heaven', 6584: 'outside', 6585: 'seeing', 6586: 'estranged', 6587: 'indifference', 6588: 'kill', 6589: 'powerful', 6590: 'undermine', 6591: 'flailing', 6592: 'la', 6593: 'alky', 6594: 'scratching', 6595: 'bye', 6596: 'jobless', 6597: "cheerin'", 6598: 'milhouses', 6599: 'rotch', 6600: 'milhouse_van_houten:', 6601: 'krabappel', 6602: 'grain', 6603: 'located', 6604: "son's", 6605: 'thoughtful', 6606: 'hairs', 6607: 'bump', 6608: 'stirrers', 6609: 'denver', 6610: 'rig', 6611: 'pad', 6612: 'waking-up', 6613: 'comedies', 6614: 'laughs', 6615: 'sistine', 6616: 'violations', 6617: 'predictable', 6618: '3rd_voice:', 6619: 'brewed', 6620: 'only', 6621: 'admiration', 6622: 'twenty-six', 6623: 'mater', 6624: 'lotsa', 6625: 'products', 6626: "yieldin'", 6627: 'sunglasses', 6628: 'enterprising', 6629: 'c', 6630: 'ignoring', 6631: 'blows', 6632: 'tow-talitarian', 6633: 'dice', 6634: 'ya', 6635: 'savvy', 6636: 'dash', 6637: 'contest', 6638: 'blurbs', 6639: "cupid's", 6640: 'los', 6641: 'boned', 6642: 'squabbled', 6643: 'branding', 6644: 'breathless', 6645: 'airport', 6646: 'pews', 6647: 'double', 6648: 'bedtime', 6649: 'tsk', 6650: 'brotherhood', 6651: 'met', 6652: 'sobo', 6653: 'thanks', 6654: 'crestfallen', 6655: 'entrance', 6656: 'buffet', 6657: 'years', 6658: 'kicks', 6659: 'totally', 6660: 'nearly', 6661: 'piano', 6662: 'dance', 6663: 'male_inspector:', 6664: 'eaters', 6665: 'blinded', 6666: 'planning', 6667: 'unbelievable', 6668: 'dishonor', 6669: 'jacksons', 6670: 'rat-like', 6671: 'oughtta', 6672: 'routine', 6673: 'calling', 6674: 'mudflap', 6675: 'surprised', 6676: "homer's", 6677: 'patented', 6678: 'ziff', 6679: 'meteor', 6680: 'upbeat', 6681: 'cola', 6682: 'hugh:', 6683: 'eightball', 6684: 'courage', 6685: 'ignorant', 6686: 'mocking', 6687: 'darjeeling', 6688: "she's", 6689: 'friend', 6690: 'marge', 6691: 'tremendous', 6692: 'perverted', 6693: 'boggs', 6694: 'worst', 6695: 'macho', 6696: 'joined', 6697: 'trip', 6698: 'hike', 6699: "shootin'", 6700: 'self-made', 6701: 'flying', 6702: 'reading:', 6703: 'fighter', 6704: 'heliotrope', 6705: 'cowardly', 6706: 'eaten', 6707: 'funny', 6708: 'kills', 6709: 'comic_book_guy:', 6710: 'odor', 6711: 'fletcherism', 6712: 'seriously', 6713: 'band', 6714: 'slop', 6715: 'rather', 6716: 'barney', 6717: 'used', 6718: 'octa-', 6719: 'tapered', 6720: 'erasers', 6721: 'researching', 6722: 'specials', 6723: 'clean', 6724: 'badmouths', 6725: 'sexton', 6726: 'rules', 6727: "mecca's", 6728: 'salad', 6729: 'moon', 6730: 'great', 6731: 'lush', 6732: 'beeps', 6733: "thinkin'", 6734: 'applesauce', 6735: 'soot', 6736: 'oh-ho', 6737: 'standing', 6738: 'money', 6739: 'genuinely', 6740: 'rag', 6741: 'princesses', 6742: 'celebrate', 6743: 'sheet', 6744: 'greetings', 6745: '7g', 6746: 'lose', 6747: 'gruesome', 6748: 'file', 6749: 'bail', 6750: 'flew', 6751: 'fountain', 6752: 'dee-fense', 6753: 'prettiest', 6754: 'chub', 6755: 'affects', 6756: 'whaaaa', 6757: 'rancid', 6758: 'humiliation', 6759: "tomorrow's", 6760: 'irs', 6761: 'moustache', 6762: 'fire', 6763: 'awareness', 6764: 'asks', 6765: 'installed', 6766: 'complicated', 6767: 'doom', 6768: 'wa', 6769: 'festival', 6770: 'soaked', 6771: 'shores', 6772: 'bus', 6773: "brady's", 6774: 'conditioners', 6775: 'vampires', 6776: 'carlotta:', 6777: 'lifters', 6778: 'closet', 6779: 'begging'}
{'sweetly': 0, 'silence': 3435, 'suave': 1, 'compliment': 2, "'cept": 3361, 'stengel': 6269, 'escort': 3, 'bag': 3437, 'add': 4, 'easter': 5, 'her': 6, 'inherent': 7, 'dreamy': 8, '_burns_heads:': 9, 'glamour': 3439, 'full-blooded': 10, 'perfected': 3440, 'by': 11, 'dingy': 14, 'shaker': 13, 'villanova': 2881, 'training': 3442, 'east': 15, 'filed': 3443, 'playoff': 16, 'tummies': 5467, 'bitterly': 17, 'duffman:': 3445, 'officer': 18, 'kearney_zzyzwicz:': 19, 'wrong': 20, 'mad': 21, 'looking': 22, 'between': 3992, 'heather': 5119, 'cuz': 3449, 'anxious': 23, 'gore': 3450, 'avec': 3451, 'lied': 4572, 'amiable': 3995, 'ugly': 3452, 'wreck': 24, 'drank': 25, 'doll': 3453, "'bout": 26, 'teenage_barney:': 27, 'releasing': 6274, 'splattered': 3454, 'unlucky': 28, 'another': 29, 'fink': 3457, 'dealer': 30, 'smile': 591, 'excuses': 3458, "stallin'": 593, 'courthouse': 31, 'ninety-nine': 32, 'writers': 33, 'aerospace': 2355, 'pall': 3459, 'puff': 6168, 'ugh': 3460, 'kermit': 3461, 'older': 3462, 'goodbye': 3463, 'pause': 3464, 'basement': 3465, 'however': 3466, 'sedaris': 34, 'forty-nine': 35, 'teenage_homer:': 3467, "can't-believe-how-bald-he-is": 36, 'admirer': 37, 'mount': 3468, 'forgive': 38, 'reasonable': 3469, 'sodas': 3470, 'diddilies': 597, 'joining': 39, 'runs': 40, 'tons': 41, 'loss': 3473, 'socratic': 4575, 'knees': 42, 'gags': 3474, 'unsourced': 3475, 'awful': 3476, 'kind': 43, 'enemy': 3477, 'deli': 6280, 'yourself': 3479, 'malabar': 1173, 'speak': 3480, 'judge_snyder:': 3481, 'red': 5699, 'benjamin:': 45, 'liable': 3482, 'onions': 46, 'uglier': 1175, 'darkness': 3484, 'did': 3485, 'buttons': 47, 'were': 3488, 'babar': 48, 'kucinich': 49, 'horrified': 3489, 'impeach': 5701, 'factor': 50, 'arts': 600, 'op': 52, 'thighs': 53, 'beatings': 54, 'tomahto': 56, "pickin'": 57, 'laughing': 1178, 'explaining': 58, 'steal': 1170, 'greetings': 6744, 'dreams': 602, 'radioactive': 3491, 'rug': 59, "america's": 3441, 'waltz': 3492, 'conditioning': 1180, 'magic': 989, 'tree_hoper:': 3493, 'fatso': 61, 'wolfcastle': 12, 'intense': 62, 'sizes': 63, 'spanish': 64, 'grey': 5134, 'grab': 3494, 'dollars': 65, 'astrid': 279, "tester's": 66, 'this:': 67, 'cattle': 3495, 'shortcomings': 68, 'funeral': 3497, 'shelbyville': 70, 'feeling': 71, 'pouring': 72, 'furiously': 73, 'feat': 3499, 'examples': 3500, 'emotion': 3501, 'bauer': 3502, 'sitting': 74, 'youth': 75, 'seen': 5873, 'shoes': 76, 'ugliness': 77, 'pretzels': 3503, "santa's": 6283, 'sooo': 79, 'snail': 80, 'rob': 81, 'low-life': 83, "y'money's": 84, 'read': 86, 'fulla': 87, "boy's": 3506, 'p': 88, 'woman': 3507, 'sesame': 89, "man'd": 3508, 'guys': 3509, 'bid': 604, 'anyone': 90, 'enough': 3510, 'hmf': 91, 'contract': 3511, 's': 92, 'anything': 3513, 'beefs': 3516, 'is': 93, 'grains': 95, 'eve': 1185, 'insist': 96, 'ever': 97, 'are': 98, 'otherwise': 3517, 'exchange': 100, 'gee': 101, 'cable': 6286, 'here-here-here': 3518, 'read:': 3519, 'cars': 3520, 'whisper': 4585, 'rebuilt': 102, 'throats': 6582, 'accident': 3521, 'peppy': 3522, 'superdad': 103, 'crotch': 104, 'hammock': 4011, 'hustle': 105, 'proves': 3524, 'sees/': 106, 'hibbert': 107, 'accurate': 108, 'tiger': 109, 'dictating': 3525, 'putting': 110, 'hurry': 111, 'pasta': 112, 'blank': 3448, "lefty's": 3528, 'cheat': 113, 'hearts': 6452, 'sickens': 114, 'stagehand:': 3529, 'item': 6291, 'sight-unseen': 115, 'honeys': 4974, 'fragile': 3530, 'ruby-studded': 5143, 'feast': 3531, 'generally': 116, 'photo': 117, 'ugliest': 118, 'juke': 3532, 'jeez': 6391, 'harvard': 119, 'occurred': 3533, 'paint': 120, 'hyper-credits': 121, 'standards': 3534, 'elephants': 122, 'thinks': 3537, 'in': 123, 'and/or': 3540, 'washer': 3541, 'emphasis': 5146, 'mid-seventies': 3542, 'stretches': 3543, 'lear': 3544, 'year': 3545, 'lemme': 124, 'example': 3546, 'laughter': 3547, 'ehhh': 612, "i'm-so-stupid": 3548, 'etc': 3549, 'scum': 2364, 'neck': 3550, 'till': 3551, 'strictly': 126, 'permitting': 4133, 'foibles': 2369, 'brooklyn': 6293, 'schabadoo': 2368, 'offa': 3553, 'broken': 3554, 'administration': 127, 'slobs': 128, 'parasol': 3555, 'club': 3556, 'cecil_terwilliger:': 3557, 'wild': 5521, 'pretty': 3558, 'trenchant': 130, "he's": 2370, 'lindsay_naegle:': 131, 'ready': 132, 'went': 133, 'continuum': 134, 'wobble': 3560, 'mm-hmm': 5710, 'someday': 3561, 'plus': 135, 'solo': 6094, 'be-stainã¨d': 5712, 'do': 136, 'excitement': 137, "we're": 3563, 'good-looking': 5149, 'field': 3564, 'old-time': 138, 'chipped': 139, 'win': 140, 'tick': 141, 'almond': 3566, 'rest': 143, 'corpses': 3567, 'pontiff': 146, 'bide': 145, 'random': 148, 'shred': 149, 'raccoons': 3568, 'gibson': 3569, 'swings': 3570, 'wind': 151, "who's": 3571, 'deacon': 3572, 'nail': 5152, 'reflected': 3455, "toot's": 152, 'frightened': 153, 'poulet': 3573, 'coherent': 154, 'drug': 155, 'deer': 156, 'sponsor': 3574, 'barbed': 2905, 'tape': 158, 'quarry': 159, 'cecil': 161, 'tonight': 162, 'composite': 163, 'switched': 166, 'available': 3575, "'em": 167, "sippin'": 168, 'gentlemen': 3576, 'decision': 169, 'lobster': 170, 'forced': 171, 'opening': 172, 'tied': 3577, 'show-off': 173, 'stop': 175, 'committee': 176, 'camera': 177, 'looting': 178, 'schizophrenia': 6174, 'young_homer:': 5155, 'lurleen': 6296, 'stamp': 179, '||exclamation_mark||': 5636, 'big': 181, 'premiering': 182, 'videotaped': 3580, 'earth': 183, 'champ': 184, 'knit': 3581, 'tigers': 3582, 'into': 5156, 'drollery': 185, 'celeste': 6297, 'ding-a-ding-ding-ding-ding-ding-ding': 186, 'endorsed': 187, 'guttural': 3583, 'cab_driver:': 3584, "betsy'll": 189, 'of': 3586, 'folks': 190, 'waylon': 2864, 'wolveriskey': 6256, 'floor': 3587, 'moxie': 3213, 'byrne': 625, 'opens': 3588, 'further': 191, 'though': 192, 'miserable': 6486, 'cheated': 193, 'muffled': 3590, 'upon': 194, 'oof': 195, 'few': 628, 'whoa': 196, 'try': 3592, "life's": 197, 'agent_johnson:': 3593, 'patriotic': 198, 'ran': 3594, 'befouled': 199, 'join': 3596, 'occurs': 3597, 'chastity': 3598, 'owner': 200, 'menlo': 201, 'trusted': 3601, 'holding': 202, 'ultimate': 3602, 'securities': 3603, 'trivia': 203, 'depressant': 204, 'guest': 3604, 'cushions': 206, 'wikipedia': 207, 'couch': 208, 'neighbor': 209, 'talk': 3607, 'musical': 211, "floatin'": 212, 'bobo': 3609, 'soaps': 213, "secret's": 3610, 'leno': 5721, 'city': 214, "g'on": 1748, 'illegal': 215, "lenny's": 216, 'befriend': 3612, 'author': 3613, 'talkative': 217, 'frog': 218, 'popular': 3614, 'passed': 219, 'moolah-stealing': 220, 'neat': 221, 'stick': 3615, 'choose': 631, 'fausto': 222, 'legend': 3616, 'beats': 223, 're-al': 224, 'portentous': 499, 'changed': 3617, 'ape-like': 3619, 'freely': 225, 'ura': 6303, 'scout': 3620, 'ruin': 226, 'men': 227, 'therapy': 3621, 'represent': 228, 'ruled': 3623, 'ivy-covered': 3625, 'upsetting': 1751, 'when-i-get-a-hold-of-you': 229, "valentine's": 3626, 'text': 3628, 'maximum': 230, 'rem': 231, 'chapel': 232, 'enjoys': 3630, 'woulda': 233, 'wood': 234, 'boyfriend': 3631, 'presided': 235, 'underbridge': 3632, 'suru': 1201, 'thrust': 1754, 'north': 236, "knockin'": 5732, 'padres': 3635, 'barney_gumble:': 237, 'rock': 5159, "wouldn't": 238, 'ladder': 6084, 'glee': 239, 'face-macer': 3897, 'chunky': 240, 'joey': 3636, 'boat': 3254, 'b': 5543, 'us': 3637, 'idioms': 5164, 'frankly': 3638, "o'problem": 241, "tab's": 242, 'walk': 3639, 'restaurants': 3640, 'mathis': 3641, 'tornado': 243, 'strolled': 244, 'wooden': 245, "cat's": 246, 'late': 3643, "what'sa": 6541, 'gregor': 247, 'inserted': 248, 'lifestyle': 249, 'poke': 3644, 'privacy': 639, 'is:': 5463, 'eyeball': 250, 'finally': 251, 'dejected_barfly:': 252, 'encores': 253, 'difficult': 254, 'caholic': 255, 'woooooo': 3645, 'moved': 3646, 'chuckle': 256, 'grenky': 3647, 'beam': 257, "haven't": 3649, 'kim_basinger:': 258, 'approval': 259, 'looser': 3650, 'kinds': 3651, 'nonsense': 3652, 'proof': 260, 'ho-ly': 3654, 'starve': 261, 'refreshingness': 262, 'lorre': 263, 'dramatic': 264, "where'd": 3656, 'one': 3657, 'lot': 3658, 'hems': 3659, 'yard': 265, 'familiar': 3660, 'improv': 3661, 'those': 4036, 'eddie': 4604, 'fixed': 3662, 'tune': 3663, 'therefore': 266, 'heaving': 6310, 'disco': 267, 'young_barfly:': 3666, 'saturday': 3667, 'space': 268, 'agents': 3668, 'vincent': 5964, 'refill': 269, 'gold': 270, 'delicately': 271, 'whatchamacallit': 272, 'arguing': 273, 'sex': 274, 'together': 3670, 'pizza': 3671, 'twenty-two': 3672, 'clone': 3673, 'accounta': 3674, 'thanks': 6653, 'ordered': 275, 'nickels': 3676, 'vomit': 3677, 'majority': 276, 'urban': 277, 'achebe': 278, "money's": 3680, 'organ': 5741, 'chance': 3681, 'suing': 280, 'manboobs': 281, 'skydiving': 3682, 'clams': 3683, 'touches': 3684, 'nursemaid': 282, 'having': 3685, "wait'll": 2389, 'ew': 4668, 'balls': 283, 'shreda': 284, 'brunswick': 285, 'brings': 5354, 'cute': 3686, 'dignified': 286, 'arrest': 3687, "ball's": 287, 'ref': 3689, 'decided': 3690, 'stood': 3691, 'quadruple-sec': 4609, 'these': 3692, "narratin'": 3693, 'batmobile': 288, 'el': 289, 'bronco': 3694, 'bart_simpson:': 5651, 'chief_wiggum:': 3695, 'eddie:': 5746, 'as': 291, 'entering': 6316, "something's": 3696, 'teach': 3698, 'persia': 3699, 'witty': 3700, 'pets': 3701, 'ocean': 3702, 'tv_announcer:': 292, 'tab': 293, 'patting': 3704, 'who-o-oa': 294, 'poor': 3706, 'destroyed': 5176, 'grease': 1272, 's-a-u-r-c-e': 3707, 'slit': 296, "tinklin'": 5178, 'tv_wife:': 3708, 'wondering': 298, 'enemies': 3710, 'boston': 3711, 'exquisite': 3712, 'fishing': 3713, 'apart': 1217, 'lovers': 300, 'value': 6577, 'incriminating': 301, 'allowed': 3714, 'reptile': 6125, 'needed': 3715, 'began': 3716, 'luckiest': 3717, 'vote': 5818, 'stepped': 3718, 'slurps': 303, 'delivery': 3719, 'less': 6319, 'eva': 304, 'nelson_muntz:': 3720, 'pyramid': 305, 'duh': 3721, 'sweat': 3722, 'languages': 3723, 'saw': 306, 'amount': 3478, 'trench': 3724, 'toy': 651, 'low-blow': 3726, 'flush': 3731, 'gals': 3728, "men's": 307, 'genius': 308, 'o': 310, 'intoxicants': 5750, 'lizard': 3606, 'seething': 3735, 'religious': 5180, 'germans': 3737, 'duke': 3738, 'grunt': 3740, 'moonlight': 3741, 'gardens': 4045, 'life-partner': 3742, 'deal': 655, 'ees': 311, 'cannoli': 312, 'fella': 314, 'domed': 3743, 'alcoholic': 3745, 'itchy': 316, 'model': 1219, 'runt': 317, 'loan': 5752, 'snapping': 318, 'intoxicated': 319, 'voice_on_transmitter:': 3748, 'groin': 1221, 'luck': 3749, 'severe': 44, 'ha-ha': 3750, '8': 3751, 'america': 3752, 'probably': 320, 'drawing': 5184, 'settlement': 5835, 'gol-dangit': 3753, 'ate': 3754, 'bear': 3755, 'lowering': 3756, 'anywhere': 322, 'forward': 323, 'jelly': 3483, 'rather': 6715, 'clearly': 324, 'devastated': 4048, 'pleading': 3758, 'god': 4613, 'modestly': 3759, 'fight': 3760, 'loser': 325, 'nineteen': 3761, 'vacations': 3763, 'strategy': 3764, 'slapped': 326, 'producers': 327, "nixon's": 3765, 'politician': 328, 'groveling': 329, 'making': 330, 'wednesday': 331, 'richer': 3486, 'have': 3766, 'sec_agent_#1:': 3767, 'audience:': 3768, 'fighting': 3769, 'choked': 334, 'flashing': 337, 'form': 336, 'hero': 3771, 'dammit': 338, 'like': 339, 'beer:': 3772, 'twenty-six': 6622, 'smoker': 340, 'pal': 341, 'hardy': 3773, 'trustworthy': 344, 'pictured': 5187, 'cruise': 345, 'mini-beret': 3774, 'midge': 3775, 'smuggled': 3776, 'choices:': 2982, 'suspenders': 2038, 'artist': 3778, 'someplace': 3779, 'traditions': 346, 'answers': 3780, 'expert': 347, 'bed': 348, 'watched': 3781, 'booth': 6446, 'steely-eyed': 2932, 'stinks': 349, 'capitalists': 3783, 'fry': 6362, 'dumbest': 351, 'different': 352, 'brakes': 354, 'xx': 3785, 'color': 355, 'gator:': 6048, 'alter': 1781, 'evasive': 357, 'donate': 6329, 'before': 359, 'montrer': 360, 'billion': 4618, 'icelandic': 361, 'too': 363, 'children': 364, 'urine': 366, 'kissed': 367, 'idea': 3787, 'conclusions': 5193, 'slot': 3788, 'ideal': 2936, 'blood': 3789, 'minors': 3791, 'leftover': 3792, 'along': 368, 'rump': 370, 'air': 3794, 'impressed': 371, 'righ': 3796, 'thank': 3797, 'putty': 372, 'yoo': 3799, 'might': 3800, 'shall': 373, 'thought': 374, 'lights': 3802, 'and': 375, 'frenchman': 376, 'arm-pittish': 3803, "don'tcha": 377, 'hospital': 378, 'quickly': 3804, 'banquo': 55, 'invented': 379, 'beards': 6332, 'eggs': 380, 'far': 381, 'swimming': 5459, 'reporter': 3805, 'rainier': 382, 'jokes': 383, "snappin'": 384, 'typing': 385, 'wad': 386, 'juan': 3490, 'total': 389, 'sam:': 3806, 'eager': 3807, 'each': 390, 'huhza': 3808, 'miss_lois_pennycandy:': 5810, 'exit': 3809, 'drunkening': 3810, 'forehead': 391, 'jacques': 5708, 'four': 5197, 'disturbance': 3811, 'covering': 392, 'squad': 669, 'albeit': 3812, 'murdoch': 393, 'feet': 394, 'forget-me-shot': 3813, 'inches': 3814, 'newspaper': 395, "neat's-foot": 396, 'bust': 397, 'supplying': 398, 'spare': 3818, 'graveyard': 3819, 'marguerite:': 400, 'martini': 401, 'ehhhhhh': 402, 'stays': 403, 'hope': 3820, 'stars': 404, 'gentles': 405, 'dirt': 3823, 'glove': 406, 'incredible': 407, "'now": 2944, 'happier': 3825, 'kisser': 408, 'suicide': 3826, 'apron': 409, 'gentle': 3827, 'manjula': 60, 'ripcord': 3829, 'truck_driver:': 3831, 'young': 410, 'book_club_member:': 411, 'service': 3833, 'civilization': 3834, 'arm': 412, 'knows': 3835, 'helped': 413, 'sighs': 414, 'relative': 5768, 'stalking': 416, 'missed': 3836, 'windowshade': 417, 'kinda': 418, 'frustrated': 419, 'force': 3837, 'girls': 5198, 'barter': 420, 'away': 421, 'superpower': 3793, 'chinese_restaurateur:': 422, 'alva': 423, 'players': 424, 'trees': 425, 'test-lady': 3840, 'roof': 3841, 'sadistic_barfly:': 3842, 'sometimes': 3843, 'salt': 428, 'pit': 429, 'larry:': 3844, 'puke': 430, 'saget': 431, 'hate-hugs': 3845, 'liquor': 5203, 'commanding': 3846, 'extreme': 2411, 'wittgenstein': 3847, 'demo': 432, 'roach': 433, 'cutie': 3848, 'charged': 3849, 'cotton': 3850, 'score': 3851, 'shut': 3852, 'comic_book_guy:': 6709, 'yup': 3853, "goin'": 3854, 'whose': 434, 'sober': 436, 'bury': 3436, "pressure's": 3855, 'roz': 438, 'why': 439, 'radical': 3856, "friend's": 1240, 'reluctant': 3857, 'cheryl': 440, "'er": 441, 'conclude': 1242, 'swear': 442, 'oww': 443, 'they': 3858, 'belches': 444, 'experienced': 3496, 'disappointment': 445, 'tiny': 69, 'successful': 446, 'sustain': 5773, 'reluctantly': 3862, 'clinton': 449, 'danish': 450, 'yea': 451, 'growing': 452, 'candidate': 3863, 'babies': 453, 'scam': 454, 'tv': 3865, 'booger': 455, 'coma': 3867, 'guiltily': 456, 'carb': 457, 'louisiana': 3868, 'brawled': 458, 'temper': 1792, 'thoughtfully': 3869, 'domestic': 459, 'mafia': 3870, 'earpiece': 460, 'plans': 461, 'mm': 3871, 'wound': 462, 'coincidentally': 463, 'playful': 3872, 'clipped': 464, 'here': 465, 'quick-like': 468, 'look': 467, 'closer': 3498, "someone's": 3873, 'gangrene': 3874, 'message': 469, 'piece': 3876, 'meals': 470, 'tyson/secretariat': 471, 'lard': 472, 'tear': 3877, 'selective': 3879, 'super-tough': 3880, 'men:': 473, 'noticing': 4634, 'brilliant': 474, 'women': 3881, 'roy': 475, 'touched': 4636, 'crinkly': 3882, 'ineffective': 476, 'bucks': 2420, 'nauseous': 477, "'im": 3884, 'page': 3886, 'mayor_joe_quimby:': 478, 'fever': 3887, 'even': 3888, "gettin'": 3889, 'dinner': 479, 'later': 1249, 'results': 480, 'anonymous': 481, 'taken': 3894, 'comic': 3895, 'bar': 482, 'homesick': 483, 'winning': 3899, 'teen': 3898, "c'mon": 486, 'sideshow_mel:': 487, 'wish-meat': 488, 'hundreds': 489, 'abcs': 1434, 'still': 3900, 'damage': 1990, 'madman': 490, 'shoot': 491, 'shock': 3901, 'grade': 492, 'acronyms': 3902, 'exhale': 4780, 'bets': 687, 'bills': 3903, 'bake': 3904, 'softer': 78, 'counterfeit': 493, "burnin'": 4641, 'prayers': 3905, 'shyly': 494, 'many': 495, 'cheery': 6313, 'championship': 3679, 'which': 3504, 'forbidden': 496, 'drapes': 3505, 'squeals': 3906, 'rascals': 497, 'letters': 142, 'pudgy': 498, 'remembering': 5212, 'killarney': 3907, 'shopping': 3908, 'who': 3629, 'mini-dumpsters': 3909, 'bono:': 3910, 'helicopter': 3824, 'spotting': 3911, 'drown': 500, 'lump': 6205, 'digging': 3912, 'nothing': 3913, 'drederick': 3914, 'launch': 501, 'clothespins:': 6245, "'topes": 503, 'santa': 504, 'saying': 6505, 'fat-free': 505, 'find': 506, 'pointing': 508, 'get': 509, 'junior': 3915, 'noble': 510, 'crushed': 3916, 'steak': 511, 'hammy': 3917, 'true': 512, 'sang': 3918, 'doing': 1251, 'guilt': 3919, 'extra': 3920, 'yourse': 3921, 'wussy': 514, 'reentering': 3922, 'caught': 3923, "spyin'": 3925, 'whole': 516, 'consulting': 517, 'increased': 519, 'k-zug': 3928, 'next': 521, 'notably': 5784, 'doy': 522, 'feel': 3929, 'marge_simpson:': 523, 'temple': 3930, 'wants': 524, 'greatly': 3931, 'surgeonnn': 1254, 'cents': 3933, 'kings': 3934, 'presidents': 3935, "ya'": 3936, 'front': 1805, 'fiiiiile': 3938, "bartender's": 3939, 'propose': 1257, 'positive': 528, 'acting': 526, 'additional-seating-capacity': 527, 'argue': 529, 'behind': 3940, 'costume': 531, 'twenty-nine': 532, 'special': 3942, 'sadder': 3943, 'purse': 533, 'slays': 2967, "rentin'": 535, 'jer': 1806, 'homers': 3946, 'prince': 3947, 'toward': 3948, 'medical': 536, 'hockey-fight': 3949, 'telling': 3515, 'wore': 537, 'aging': 538, 'unkempt': 3951, 'poem': 539, 'evergreen': 3952, 'intriguing': 5000, 'teenage': 541, 'poorer': 542, 'curiosity': 5678, 'huge': 543, 'friction': 544, 'officials': 545, 'honey': 3953, 'heals': 546, 'slyly': 3954, 'buffalo': 3956, 'sharps': 3957, "hangin'": 6504, 'cruel': 3958, 'guff': 3959, 'mugs': 549, 'cesss': 550, 'alfred': 3960, 'lowest': 551, 'insightful': 3962, 'elite': 3963, 'age': 3964, 'embarrassed': 3965, 'kirk_voice_milhouse:': 554, 'freak': 553, 'paper': 1261, 'quimbys:': 3967, 'tries': 3968, 'disposal': 555, 'film': 3969, 'sister': 556, 'attraction': 558, 'prettied': 3970, '4x4': 559, 'blown': 6422, 'taps': 3971, "isn't": 562, 'seductive': 563, 'endorsement': 564, 'crowbar': 565, 'relationship': 3972, 'unless': 567, 'heading': 568, 'distract': 569, 'collateral': 3973, "school's": 570, 'lodge': 3974, 'working': 571, 'four-drink': 572, 'principal': 3975, 'picture': 3976, 'lighting': 3977, 'donor': 574, 'sentimonies': 575, 'maya': 3981, 'invisible': 3979, 'composer': 3980, 'shape': 576, "'til": 2972, 'sen': 577, 'boozebag': 578, 'champion': 3983, 'umm': 579, 'hooters': 580, "tv's": 581, 'poking': 3986, 'yammering': 3987, 'shill': 6359, 'blame': 3988, 'depression': 99, 'information': 5794, 'take': 584, 'somewhere': 3608, "bein'": 5795, 'exasperated': 3989, 'professor_jonathan_frink:': 3990, 'gin-slingers': 585, 'terrible': 3991, 'contented': 586, 'comes': 587, 'earlier': 3993, 'behavior': 3994, 'computer': 588, 'amused': 5224, 'okay': 589, 'palmerston': 590, 'three-man': 3996, 'exploiter': 5225, "tv'll": 594, 'weak': 3997, 'step': 3599, 'tofu': 3998, 'poker': 595, 'became': 596, 'whatchacallit': 598, 'medieval': 3999, 'ride': 4818, 'real': 599, 'killing': 4000, 'problem': 601, 'cuckoo': 603, 'fail': 2442, 'rickles': 4002, 'manchego': 4657, 'loved': 4003, 'strategizing': 4004, 'said': 4005, 'nahasapeemapetilon': 4006, 'lookalike:': 4007, 'gasoline': 605, 'spy': 2444, 'influence': 4008, 'head': 606, 'panties': 1270, 'rookie': 607, 'daaaaad': 608, 'lurks': 6562, 'grants': 4009, 'wham': 4010, 'and:': 610, 'squeal': 611, 'history': 4012, 'betcha': 4013, "usin'": 4014, 'eats': 4015, 'nonchalant': 613, "singin'": 4016, 'partly': 4017, 'failed': 4018, 'neither': 614, 'channel': 615, 'hunger': 4019, 'chuckling': 616, 'grandmother': 4020, 'blob': 619, 'crimes': 618, 'penmanship': 3523, 'system': 2873, 'moments': 4022, 'embarrassing': 621, 'geyser': 622, 'engine': 4023, "it'd": 4024, 'hollywood': 623, 'dejected': 624, 'grimly': 4025, 'pub': 626, 'reads': 629, 'broken:': 5228, 'butts': 4026, 'compadre': 630, 'semi-imported': 632, 'milks': 633, 'worldview': 634, "renovatin'": 4027, 'jã¤germeister': 4028, 'soul': 4029, 'suspiciously': 4030, 'yee-haw': 635, 'schmoe': 6565, 'skinner': 5231, 'neighbors': 636, 'koji': 637, 'excavating': 638, 'steinbrenner': 4032, 'treasure': 4033, 'nap': 640, 'saint': 4034, 'cake': 704, 'pants': 4037, 'feed': 1274, 'frat': 641, 'banquet': 642, 'also': 6368, 'fevered': 644, 'caused': 4039, 'slim': 645, 'frazier': 646, 'woodchucks': 647, 'ehhhhhhhhh': 648, 'liven': 4040, 'drunkenly': 649, 'arrested:': 650, 'peabody': 4041, 'snide': 4042, 'dress': 652, 'beach': 4044, 'known': 653, 'encore': 654, 'perking': 4046, 'bones': 656, 'touchdown': 886, 'carefully': 5233, 'kiss': 657, 'words': 658, 'punishment': 708, 'advantage': 659, 'serious': 4047, 'adeleine': 5234, 'considers': 4049, 'appreciated': 660, 'glen': 4050, 'manatee': 4051, 'dr': 520, 'dark': 661, 'carpet': 662, 'enter': 3526, 'artie_ziff:': 1654, 'discuss': 6373, 'church': 663, 'presumir': 4053, 'broncos': 4054, 'believe': 664, 'coaster': 4056, 'goodwill': 665, 'uses': 666, 'firing': 667, "hell's": 4057, 'leathery': 668, "speakin'": 670, 'dramatically': 4058, 'whispers': 671, 'ne': 4059, 'investor': 672, 'nightmares': 6374, 'easier': 4060, 'jigger': 673, 'spender': 3527, "makin'": 4061, 'boozehound': 5805, 'disillusioned': 674, 'dive': 4062, 'slobbo': 4063, 'held': 1699, 'universe': 4064, 'thumb': 677, "soundin'": 678, 'apu': 679, 'savings': 680, 'congratulations': 4066, 'captain:': 4067, "scammin'": 681, 'crew': 5237, 'troy_mcclure:': 682, 'bridge': 683, 'toms': 684, "industry's": 685, 'brother-in-law': 686, 'peppers': 4068, 'pulling': 4069, 'ninety-six': 4070, 'watered-down': 4071, 'treehouse': 688, 'buddies': 689, 'iddilies': 4072, 'wacky': 4073, 'tease': 690, "doctor's": 4074, 'started': 691, 'betty:': 692, 'glorious': 5238, 'eco-fraud': 4075, 'alma': 693, 'hotel': 694, 'beings': 695, 'eye-gouger': 696, 'swigmore': 4078, "kiddin'": 4079, 'cockroach': 4080, "shouldn't": 697, 'kansas': 698, 'irishman': 4081, 'connor-politan': 4082, 'warren': 4083, 'mistakes': 4084, 'jail': 2941, 'jerk': 4085, "liftin'": 699, 'multi-purpose': 700, 'partially': 4086, 'skoal': 4087, 'romantic': 4088, 'eggshell': 1283, 'pepto-bismol': 4089, 'kramer': 701, 'high': 2987, 'flashbacks': 4090, "'your": 4091, "it's": 4092, 'drift': 1066, 'bellyaching': 702, 'million': 4093, 'inspector': 703, 'finished': 4669, 'dipping': 705, 'microwave': 706, 'chairman': 707, 'pointedly': 4094, 'nameless': 709, 'guzzles': 4095, 'teacup': 6087, 'backward': 710, 'granted': 4096, 'mix': 4097, 'mild': 711, 'drop': 1284, ':': 4098, 'deliberately': 712, 'smallest': 714, 'blaze': 4099, 'masks': 4101, 'lindsay': 2455, 'narrator:': 4102, 'vacation': 4674, 'everywhere': 715, 'nein': 4103, 'drummer': 716, 'fourth': 4107, 'milk': 718, 'moe-clone:': 719, 'looks': 720, 'heavyset': 721, 'half-day': 4108, 'motorcycle': 722, 'pronto': 4109, 'tow-joes': 723, "tryin'": 724, 'giving': 3189, 'flowers': 725, 'fayed': 6380, 'pages': 5241, 'liability': 1288, 'bring': 5242, 'half-back': 726, 'cerebral': 4105, 'career': 728, 'sniper': 729, 'brusque': 730, 'cannot': 4106, "playin'": 4115, 'mug': 4116, 'halloween': 731, 'peach': 4117, 'yuh-huh': 732, 'lenses': 734, 'inspire': 4119, 'expensive': 735, 'shuts': 736, 'rolled': 5243, 'quarter': 1291, 'reporter:': 737, 'abusive': 5245, 'christmas': 4120, 'museum': 738, 'boyhood': 3535, 'up': 4122, 'ali': 2459, 'specified': 739, 'shirt': 740, 'evils': 4123, 'squeeze': 3536, 'burps': 741, 'bragging': 4125, 'barkeeps': 4126, 'spouses': 742, 'napkins': 743, 'sales': 1293, 'marvelous': 744, 'option': 5816, 'man:': 745, 'shutting': 4130, "swishifyin'": 746, 'anyhow': 4131, 'hooray': 3538, 'hillbillies': 747, 'cheering': 748, 'rat': 749, 'rats': 3539, 'shipment': 750, 'aggravazes': 4134, 'toledo': 4135, 'edna-lover-one-seventy-two': 6383, 'anymore': 751, 'hall': 5249, 'dank': 5831, "department's": 4137, 'impending': 4138, '70': 752, 'bail': 6749, '7-year-old_brockman:': 4140, 'me': 753, 'quimby': 754, 'winner': 4141, 'tidy': 756, 'alphabet': 757, 'breathalyzer': 4110, 'cavern': 4142, 'broke': 4143, 'agree': 758, 'characteristic': 4144, 'struggling': 759, 'er': 760, 'waitress': 4145, 'crummy': 4146, 'bartenders': 4147, 'supermodel': 761, 'anarchy': 4148, 'sixty-five': 4149, 'jams': 762, 'scream': 4150, 'unable': 4151, 'spectacular': 4152, 'hangs': 763, 'family': 4153, 'swimmers': 764, 'unbelievably': 4111, 'murmurs': 2157, 'preparation': 4112, 'gayer': 1296, 'given': 727, 'ned': 4155, 'boxcars': 765, 'caper': 6388, 'enthusiastically': 4156, 'david_byrne:': 5829, 'actors': 4157, 'nerd': 4158, 'grunts': 4159, 'homeless': 766, 'killer': 5828, 'angrily': 767, 'falling': 125, 'traitors': 768, 'nonchalantly': 4113, 'massive': 769, 'jewelry': 770, 'audience': 6572, 'dory': 771, 'bleak': 4162, 'bush': 4163, 'pocket': 4164, 'haw': 6389, 'hollye': 772, 'deserve': 4165, 'deals': 1846, 'pancakes': 4166, 'obsessive-compulsive': 4167, 'grand': 4168, "dolph's_dad:": 773, 'fired': 774, 'punch': 4169, 'tips': 775, 'bursts': 6390, 'broad': 776, 'enjoyed': 780, 'entire': 778, "i'd'a": 779, 'ping-pong': 4172, 'honor': 4174, 'shorter': 781, 'sued': 3002, 'news': 4175, 'sucker': 782, 'yap': 4176, 'exactly': 4177, "let's": 783, 'ninety-eight': 733, 'eighty-five': 2316, 'employment': 4179, 'peanuts': 786, 'contemplated': 4181, 'lily-pond': 787, 'presses': 788, 'forgiven': 4184, 'remember': 4183, "ma's": 789, 'chilly': 4185, 'tv_father:': 790, 'actually': 791, 'stopped': 5832, 'skinny': 4187, 'stripe': 792, 'ram': 4188, 'happily': 5833, 'apu_nahasapeemapetilon:': 793, 'unusual': 794, "g'ahead": 795, 'crumble': 4189, 'both': 4190, 'shush': 6487, 'whatsit': 797, 'clapping': 2470, 'eighty-six': 798, 'incognito': 799, 'plug': 800, 'tin': 4192, 'director': 801, 'mckinley': 4193, 'remodel': 802, 'eliminate': 3552, 'effects': 4195, 'coy': 803, 'oak': 804, 'knock-up': 4196, 'a-b-': 4197, "comin'": 805, 'justice': 5257, 'decency': 806, 'grope': 4201, 'afternoon': 807, 'heavens': 4202, 'verdict': 5836, 'formico': 808, 'break': 4203, 'has': 810, 'competing': 812, 'renovations': 5260, 'understanding': 4205, 'businessman_#1:': 813, 'ding-a-ding-ding-a-ding-ding': 815, 'rolling': 816, "clancy's": 818, 'mmmm': 4206, 'complaining': 1304, 'weight': 4207, 'marmaduke': 6385, 'build': 819, 'stole': 5496, 'stayed': 820, 'title': 1853, 'goal': 5838, 'pills': 1306, 'doubt': 6207, 'drinker': 821, 'partner': 822, 'handling': 4209, 'items': 4210, 'stocking': 823, 'romance': 824, 'voted': 4212, 'reserved': 4213, 'learn': 4121, 'kemi:': 825, 'count': 3007, 'aged': 4215, 'principles': 826, 'meaning': 4216, 'pour': 827, 'anybody': 4217, 'midnight': 4218, 'kemi': 4219, 'helpless': 129, 'alley': 828, 'urinal': 4220, 'pigtown': 830, 'attractive_woman_#2:': 4222, 'roll': 4223, 'square': 1612, 'notices': 4225, 'oughtta': 6671, 'mumbling': 831, 'extract': 832, 'seymour_skinner:': 833, 'vin': 4226, 'wise': 834, 'warmth': 6204, 'potato': 4227, "gentleman's": 835, '_montgomery_burns:': 4229, 'right-handed': 4230, 'wears': 4124, '$42': 4231, 'minimum': 5267, 'priority': 5018, 'app': 4232, 'doppler': 4233, 'nick': 3559, 'hank_williams_jr': 836, 'm': 4235, 'zeal': 1859, 'microbrew': 6224, 'comfortable': 4236, 'wade_boggs:': 4237, 'phasing': 838, 'steam': 839, 'johnny_carson:': 841, 'depository': 843, 'getcha': 5757, "aren'tcha": 844, 'logos': 4127, 'assumed': 4238, 'prefer': 4239, 'creeps': 4240, 'puzzled': 924, 'teeth': 4241, 'hubub': 846, 'mine': 4242, 'knuckles': 847, 'bars': 848, 'infor': 4243, 'wordloaf': 4245, 'lead': 851, 'jasper_beardly:': 4246, 'ruuuule': 853, 'trunk': 6294, 'keeping': 4247, 'nards': 4248, 'whatever': 4129, 'population': 854, 'must': 4250, 'halvsies': 855, 'vehicle': 4251, 'ho-la': 4252, 'tears': 4253, 'take-back': 4255, 'farthest': 5274, 'may': 2728, 'pack': 4256, 'operation': 4257, 'period': 4258, 'delivery_boy:': 4259, 'agh': 857, 'anyhoo': 4260, 'fifth': 4261, 'spelling': 4262, 'served': 4263, 'buzziness': 4264, 'sitar': 858, 'whaddaya': 859, 'heroism': 860, 'continuing': 861, 'closes': 4265, 'settled': 4266, 'meaningful': 1318, "wino's": 4267, 'baby': 5688, 'rector': 4268, 'side:': 862, 'jackass': 4269, 'mention': 863, 'arimasen': 864, 'unfair': 865, "o'": 4271, 'tearfully': 4272, 'anderson': 866, 'maya:': 4273, 'wear': 867, 'test-': 4274, 'quietly': 4275, 'smug': 4276, 'booze': 5846, "fryer's": 3021, 'bottomless': 868, 'liver': 4277, 'favor': 4280, 'meeting': 4279, 'adequate': 4132, 'had': 3562, 'moonnnnnnnn': 5848, 'trying': 869, 'event': 82, 'mister': 3024, 'regretful': 1863, 'grienke': 870, 'expense': 871, 'stay': 872, 'mommy': 873, 'finest': 3565, 'mines': 874, 'unrelated': 4282, 'college': 4283, 'obese': 5232, 'municipal': 4136, 'paintings': 875, 'smiled': 4284, 'inclination': 4285, 'lipo': 876, 'pass': 4286, 'princess': 4287, 'spitting': 4288, 'lovejoy': 5850, 'afford': 4289, 'warranty': 877, 'leak': 880, 'insulted': 879, 'klingon': 881, 'carmichael': 882, 'sharity': 4290, 'mindless': 4291, 'facebook': 4292, 'created': 4293, 'pope': 4294, 'express': 883, 'freaky': 884, 'para': 4296, 'sweet': 4297, 'bachelorhood': 4298, 'streetlights': 887, 'declared': 888, 'militia': 4299, 'including': 4300, 'sledge-hammer': 890, 'chili': 4301, 'iranian': 4302, 'hiya': 891, 'wienerschnitzel': 4303, 'tolerance': 895, 'scrubbing': 894, 'everyday': 755, 'maggie': 144, 'animals': 896, 'rev': 897, 'seek': 899, 'mountain': 900, 'strips': 901, 'helen': 902, 'crowned': 903, 'lonely': 904, "now's": 147, 'wing': 905, 'the': 906, 'pulled': 4307, 'joey_kramer:': 907, 'sly': 4308, 'senators': 5283, 'barney-type': 908, "hadn't": 909, 'rekindle': 1325, 'gang': 6348, 'running': 1527, 'excuse': 4310, 'high-definition': 910, 'ivory': 4311, 'button-pusher': 4001, 'itself': 953, 'olive': 4312, 'busy': 4313, 'pus-bucket': 911, 'haws': 4314, 'answering': 912, 'punk': 913, 'fustigate': 914, 'needs': 4316, 'gambler': 4317, 'pro': 4318, 'regret': 4319, 'whenever': 4321, 'european': 915, "hawkin'": 4322, 'scanning': 917, 'amazing': 4323, 'blur': 918, 'juice': 4324, 'loud': 4325, 'confidential': 4327, 'ron_howard:': 919, 'certainly': 920, 'cause': 4328, 'polenta': 4329, 'my': 4330, 'jay_leno:': 4331, 'life:': 5854, 'please/': 4332, 'slogan': 4333, 'would': 921, 'cheap': 922, 'doreen': 4335, 'swallowed': 4336, 'troll': 3034, 'ails': 150, 'sweaty': 923, 'issuing': 926, 'you': 4338, 'inspection': 1941, 'delicate': 4340, 'i/you': 5503, 'crowd': 927, 'sagely': 928, 'cheapskates': 5856, 'lincoln': 4710, 'moesy': 4342, 'accept': 930, 'following': 5858, 'viva': 931, 'lenny:': 4343, 'exhaust': 1331, 'hyahh': 4344, 'ah': 4345, 'polish': 4346, 'table': 4347, 'upgrade': 4348, 'shifty': 4349, 'filthy': 934, 'contemplates': 4352, 'speech': 4351, 'movies': 4353, 'kang:': 4354, 'hah': 4355, 'credit': 4357, 'cell': 936, 'protecting': 4358, 'slurred': 5852, 'photographer': 937, 'losers': 4359, 'under': 4360, 'level': 4362, 'patrons': 5375, 'explain': 942, "we'd": 4363, "they're": 4364, "'round": 944, 'killjoy': 4366, 'from': 945, 'clientele': 4369, "blowin'": 6352, 'yogurt': 4370, 'bastard': 4371, 'theme': 4372, 'non-american': 4373, 'woman_bystander:': 4374, 'bump': 6607, 'hourly': 4375, 'chain': 948, 'priceless': 4377, 'rent': 4379, "foolin'": 5842, 'tabs': 4380, 'milhouse': 949, 'bottles': 950, 'meanwhile': 951, 'santeria': 4381, 'envy-tations': 157, 'longer': 954, 'long': 6429, 'territorial': 955, 'pre-recorded': 4384, 'ask': 956, 'awe': 957, 'bonding': 959, 'swamp': 960, 'super-nice': 961, 'coast': 962, "number's": 4385, 'dumb': 963, 'th': 964, 'lise:': 4386, 'gimmicks': 4387, 'jacques:': 965, 'cauliflower': 4388, 'strangles': 5294, 'moon-bounce': 4390, 'lady-free': 1339, 'howya': 4391, 'yew': 967, 'encouraging': 968, 'eating': 3140, 'sickened': 970, 'depressing': 4394, 'reason': 4393, 'sperm': 971, "fendin'": 972, 'drains': 4395, 'jesus': 974, 'whistling': 165, 'newest': 4397, 'jeff_gordon:': 4398, 'reciting': 4399, 'superhero': 4400, 'heaven': 6583, 'devils:': 4401, 'mind': 6420, "must've": 4402, 'chipper': 976, 'moan': 5537, 'hmmmm': 977, 'greystash': 4403, 'chips': 4404, 'insulin': 981, 'arms': 4405, 'awww': 4406, 'benjamin': 4407, 'ditched': 984, 'alien': 983, '||comma||': 985, 'blimp': 4408, 'wiping': 986, 'steaming': 6423, '_babcock:': 987, 'barflies:': 4409, 'song': 988, 'souped': 4411, 'campaign': 4412, 'imaginary': 4413, 'leg': 1342, 'clips': 4414, 'hers': 4415, 'bunion': 4417, 'rumor': 3041, 'pre-columbian': 4418, 'exhibit': 5653, 'ridiculous': 1883, 'placing': 4419, 'buzz': 990, 'polite': 4420, "one's": 4421, 'ihop': 1589, "stealin'": 4422, 'inning': 5865, 'blissful': 4423, 'muertos': 992, 'phone': 174, 'lame': 993, 'astonishment': 994, 'nos': 2330, 'classy': 4424, 'fixes': 4425, 'blooded': 4426, 'rub': 995, 'memories': 4427, 'competitive': 4428, 'loyal': 6277, 'compressions': 5867, 'attach': 997, 'bowled': 4429, 'announcer:': 1000, 'wiggle-frowns': 1001, 'bird': 3578, 'eminence': 1002, 'handwriting': 4430, 'except': 1003, 'taste': 4431, 'coughs': 1004, 'than': 4432, 'vulnerable': 4433, 'doug:': 1005, 'department': 4435, 'symphonies': 1006, 'brown': 6027, 'rip-off': 1007, "nick's": 1008, 'strong': 1009, 'starlets': 1651, 'diminish': 4436, 'bottle': 1010, 'ancient': 1011, 'outlive': 1012, 'outstanding': 4437, 'sexual': 3438, 'plotz': 4438, 'slender': 1014, "it'll": 4439, 'taunting': 4440, 'scrutinizes': 4442, 'months': 1017, 'muscle': 1019, 'sturdy': 4160, 'shout': 1020, 'make': 4444, 'bounced': 1021, 'hi': 4447, 'upn': 1023, 'wazoo': 4448, 'buyer': 1025, 'frescas': 4449, 'things': 4450, 'thirty-three': 4451, 'fatty': 1026, 'generous': 2511, 'pillows': 4453, 'christian': 4454, "world's": 1027, 'bart': 1028, 'de': 1029, 'wheels': 4456, 'woe:': 4457, 'adjust': 4458, 'aside': 4460, 'meal': 4461, 'drove': 1032, 'swill': 1033, 'unhook': 4463, 'lennyy': 4464, 'problems': 1035, 'ball-sized': 4465, 'reviews': 4466, 'mole': 4467, "s'cuse": 1036, 'bad': 4468, 'mike_mills:': 4139, 'rings': 4469, 'scientists': 4470, 'number': 1038, 'butter': 1039, 'western': 1891, 'aggravated': 2515, 'games': 1041, 'underwear': 4472, 'shoots': 1042, 'bob': 1043, 'managing': 1044, 'kl5-4796': 1045, 'water': 1046, 'foam': 4473, 'dana_scully:': 4474, 'aristotle:': 5307, 'undermine': 6590, 'weekend': 1048, "tramp's": 1049, 'spacey': 4476, 'dials': 4979, 'appealing': 4477, 'alcohol': 1050, 'grandiose': 1052, 'yourselves': 1053, 'per': 4479, 'phase': 1054, 'state': 4254, 'muscles': 2521, 'community': 4480, 'nigerian': 4481, 'self-satisfied': 4482, 'samples': 1056, 'choked-up': 1059, 'concerned': 1058, 'hangover': 4484, 'distance': 4485, 'yours': 1060, 'flag': 188, 'freaking': 1061, 'mmm-hmm': 1062, 'tapping': 1063, 'stagey': 4487, 'hosting': 4488, 'sensible': 4489, 'permanent': 1064, 'phony': 5311, 'thing': 1067, 'son': 5310, 'payback': 4491, 'fourteen:': 3579, 'wangs': 4492, 'badmouth': 5880, 'fun': 1070, 'food': 3056, 'data': 1071, 'dozen': 4493, "plaster's": 1072, 'choice': 1073, 'island': 4495, 'stab': 4496, 'begin': 4118, 'au': 5881, 'libraries': 6434, 'duel': 4497, 'breaking': 4498, 'abe': 4499, 'ears': 4500, 'sweeter': 1074, 'anyway': 1075, 'las': 2513, 'issues': 1076, 'looooooooooooooooooong': 1079, 'realize': 1078, 'correction': 3732, 'rebuttal': 1080, 'macgregor': 1081, 'wigs': 1417, 'thousands': 1082, 'disco_stu:': 1083, 'willy': 1084, 'compared': 1085, 'judges': 4501, 'glasses': 1086, 'dint': 5259, 'scare': 4502, 'convinced': 1087, '530': 4503, 'frontrunner': 1088, 'accent': 4505, 'damn': 4506, 'angel': 1089, 'fast': 6548, 'cleaner': 1090, 'stairs': 1091, 'guy': 4507, 'stock': 4734, 'knife': 1092, 'chip': 1094, "sayin'": 4508, 'hemorrhage-amundo': 1096, 'clock': 4509, 'hoped': 3589, 'computer_voice_2:': 1100, "o'clock": 1098, 'the_edge:': 1099, 'weekly': 4511, 'self-centered': 4512, 'dae': 1103, 'measure': 1102, 'uhhhh': 1961, 'happily:': 777, 'free': 3061, 'activity': 4513, 'golf': 4514, 'karaoke_machine:': 4515, 'feedbag': 1104, 'sieben-gruben': 1105, 'simpsons': 4171, 'p-k': 4738, 'press': 4516, 'kahlua': 1108, 'seven': 1109, 'war': 4517, 'telephone': 1110, 'moustache': 6761, 'passports': 4173, 'offshoot': 4518, 'sequel': 3591, 'flaming': 4519, 'appalled': 1111, 'violin': 1112, 'loboto-moth': 1113, 'femininity': 4521, 'tempting': 1116, 'smile:': 1118, 'shrugs': 5607, 'ways': 4522, 'winded': 1119, 'grabs': 1120, 'nitwit': 1122, 'bagged': 4523, 'aghast': 4524, 'army': 1123, 'homer_': 5316, 'home': 4526, 'sneak': 1124, 'sampler': 4528, 'intention': 4529, 'necklace': 4530, 'kidneys': 4531, 'ho': 4532, 'tones': 1125, 'stir': 4779, "dyin'": 4533, 'hits': 1126, 'business': 1127, 'shindig': 1128, 'dime': 1129, 'bash': 1130, 'turlet': 6503, 'likes': 4535, 'shaved': 4536, 'chick': 4537, 'perplexed': 3595, 'st': 4538, 'talkers': 1132, 'boneheaded': 5866, 'morlocks': 4178, 'off': 1133, 'occurrence': 784, 'fifteen': 1135, 'wrote': 4539, 'thawing': 1136, 'abolish': 1137, 'ralphie': 1138, 'fast-food': 1140, 'dan': 1141, 'acceptance': 6355, 'wrecking': 4540, 'legal': 1143, 'various': 3600, 'trail': 4541, 'awed': 785, 'coward': 4542, "we'll": 5247, 'planet': 1145, 'seamstress': 4544, 'fumes': 1147, 'buying': 4545, 'headhunters': 4546, 'gift': 4547, 'disappointing': 5319, 'alternative': 6144, 'senators:': 4180, 'sport': 1149, 'surprise': 4549, 'dregs': 4550, 'cup': 1151, 'reynolds': 1152, 'puke-pail': 1153, 'barstools': 4551, 'mint': 4552, 'cock': 1154, 'and-and': 4554, 'hair': 4555, 'power': 4556, 'little': 1155, 'single-mindedness': 4557, "tatum'll": 4558, 'spilled': 1157, 'yelling': 4559, 'alibi': 1158, 'customers-slash-only': 1567, 'transylvania': 1159, 'reminded': 4561, 'ironed': 1161, 'most:': 1162, 'sudden': 4562, 'taxes': 1024, 'wells': 4563, 'homer_doubles:': 4564, 'fury': 5321, 'sniffing': 4565, 'wheel': 5280, 'mexican_duffman:': 1163, 'beauty': 4182, 'popping': 1164, 'confidentially': 1165, 'painting': 4568, "depressin'": 4745, 'habit': 1166, "somethin':": 1167, 'jobless': 6596, "smokin'_joe_frazier:": 6581, 'deliberate': 4570, 'signal': 1168, 'judge': 4571, 'lawyer': 5323, 'the_rich_texan:': 4186, 'gear-head': 3605, 'smooth': 4573, 'horrors': 205, 'protestantism': 4574, "'morning": 1171, 'go': 3075, 'nbc': 4576, 'smitty:': 1174, 'automobiles': 4579, 'unintelligent': 3382, 'edison': 1176, 'shows': 6045, 'libido': 1181, 'powered': 1179, 'specific': 4580, "doesn't": 4581, 'bourbon': 1182, 'assistant': 6443, 'nevada': 1183, 'quitcher': 1184, 'follow': 4583, 'dateline': 4584, 'swooning': 1186, 'part-time': 4586, 'dizzy': 1187, 'monkey': 2533, 'third': 1188, 'hands': 1611, 'philosophic': 4587, 'soon': 2846, 'arse': 4588, 'meant': 6010, 'bupkus': 1189, 'inquiries': 4589, 'rafter': 5127, 'checking': 4590, 'harvey': 1371, 'sit': 3446, 'ford': 1190, 'proudly': 4749, 'spit': 1191, 'strain': 1192, 'guinea': 4591, 'modest': 1193, 'edgy': 4593, 'craft': 4594, 'faceful': 1194, 'uh': 3081, 'run': 1918, 'renee': 1195, 'commission': 5893, 'blew': 1373, 'worked': 1196, 'pajamas': 1197, 'pressure': 582, 'ahh': 4595, 'ruint': 4596, 'old_jewish_man:': 4597, 'overflowing': 1198, "lady's": 1199, 'leans': 796, 'rubbed': 5326, 'bannister': 4598, 'numbers': 1200, 'handsome': 4599, 'hail': 4600, 'ripping': 4601, 'retain': 4602, 'alone': 1202, 'madonna': 1203, 'spite': 1204, 'duffman': 1205, 'longest': 4191, "buffalo's": 1206, 'bride': 4603, 'poison': 1207, 'brain': 1208, 'souvenir': 1209, 'detective': 5327, 'sun': 1210, "mtv's": 1211, 'all-american': 5898, 'absentminded': 4606, 'video': 1212, 'wildest': 4607, 'numeral': 4608, 'manage': 1213, '_zander:': 1214, "cont'd:": 6447, 'such': 1215, 'throws': 1216, 'mob': 4610, "larry's": 1218, 'tragedy': 4611, 'duty': 1220, 'die': 4612, 'sour': 1222, 'errrrrrr': 5902, 'chief': 1223, 'getaway': 3611, 'somehow': 5298, 'twenty-four': 4614, 'charming': 4615, 'cricket': 1224, 'threw': 1225, 'falcons': 4616, 'leonard': 4617, 'something': 1226, 'unfresh': 4392, 'chapstick': 1115, "fun's": 1227, 'cage': 4619, 'ambrose': 1228, 'j': 1229, 'deny': 4194, 'half-beer': 1230, 'darkest': 1231, 'for': 4620, 'courteous': 1232, 'paris': 1233, 'trapped': 4621, 'now': 1235, 'bon': 1236, 'recorded': 1237, 'introduce': 1238, 'barney-guarding': 4622, 'researching': 6721, 'satisfied': 4623, 'terminated': 4624, 'cletus_spuckler:': 1239, 'jeers': 4625, 'fresh': 4170, 'kako:': 1241, 'squirrel': 4627, 'wishes': 1243, 'we-we-we': 1244, 'relaxed': 1245, "donatin'": 4628, 'distributor': 4629, 'sail': 4630, 'bull': 4198, 'hug': 4631, 'f-l-a-n-r-d-s': 6450, 'housing': 4632, 'delete': 4633, 'represents': 1247, 'material': 4635, 'noose': 4637, 'e-z': 4199, "bart's": 1248, 'naively': 4638, 'wasted': 4639, 'selfish': 4389, 'handler': 4640, "when's": 5824, 'aidens': 4642, 'maxed': 4643, 'wishing': 1250, 'grandkids': 4200, 'twenty': 1252, 'attached': 1253, 'billiard': 609, 'witches': 4645, 'folk': 4646, 'layer': 4764, 'every': 1255, 'skunk': 4647, 'hampstead-on-cecil-cecil': 4648, 'pain': 4649, 'faced': 4650, 'statistician': 5908, 'carny:': 1259, 'offer': 1260, 'magazine': 4651, 'admit': 1263, 'finale': 5927, "poisonin'": 1264, 'donut-shaped': 1379, 'nah': 4652, "messin'": 4653, 'ancestors': 1265, 'kyoto': 2458, 'trade': 1266, "callin'": 1267, 'edelbrock': 4654, 'joe': 2545, 'abandon': 4656, 'manuel': 1268, 'beloved': 1269, 'gas': 5333, "spiffin'": 4658, 'crying': 809, 'hidden': 4659, 'drivers': 3091, 'stupidly': 1271, 'plucked': 3618, "kid's": 4204, 'maiden': 4660, 'lobster-politans': 4661, "heat's": 4662, 'check': 1273, 'finger': 4663, 'zoomed': 4664, 'absentmindedly': 1275, 'rivalry': 5911, 'managed': 1276, 'smiles': 1277, 'eight-year-old': 4665, 'moe-clone': 5335, 'vengeful': 811, 'reunion': 1278, 'bubble': 4666, 'marry': 1279, 'laney': 5500, 'flanders': 1280, 'intrigued': 1281, 'hunting': 4667, 'multi-national': 1282, 'strokkur': 4670, 'compromise:': 4671, "tony's": 2549, 'charm': 1285, 'wizard': 4672, "ol'": 1287, 'reaches': 6459, 'space-time': 4675, 'straight': 1289, 'rationalizing': 1290, 'boring': 4676, 'dies': 4677, 'homeland': 4678, 'paparazzo': 1292, 'november': 3622, 'pronounce': 4679, 'key': 4680, 'botanical': 3624, "sittin'": 5915, 'richard': 1294, 'philosophical': 4681, 'due': 4682, 'shaking': 1295, 'spend': 1297, 'scrape': 4683, 'legoland': 817, 'jerk-ass': 1298, 'it:': 1299, "how's": 4684, 'ron': 4685, 'pawed': 4686, 'desperate': 1300, 'bright': 4687, 'touch': 5340, 'any': 1301, 'mouse': 1302, 'doctor': 4688, 'sits': 4689, 'international': 4690, 'cutting': 1303, 'owe': 4691, 'k': 1305, 'lenny_leonard:': 6186, 'solid': 4692, 'grind': 1307, 'lord': 4693, 'root': 1308, 'morning-after': 5919, 'cops': 1309, 'throwing': 1310, 'psst': 1311, 'snake-handler': 4694, 'starla:': 1312, 'solely': 4208, 'mirror': 1313, 'line': 1314, 'metal': 4696, "team's": 4697, 'slipped': 1315, "ladies'": 4699, 'wayne': 4700, 'd': 4701, 'course': 4702, 'bell': 1316, 'owes': 1317, 'hoax': 1319, 'dennis_kucinich:': 4703, 'backing': 3627, 'freshened': 1320, "she'd": 1321, 'maintenance': 4705, 'voice': 1322, 'norway': 4706, 'tanking': 4707, 'laws': 2976, 'phlegm': 1323, 'ale': 713, 'die-hard': 1324, 'coach:': 4708, 'cans': 1326, 'holidays': 4709, 'updated': 1327, 'human': 5922, 'peeping': 1328, 'trashed': 1329, "handwriting's": 1330, 'friday': 5344, 'rutabaga': 4711, 'radiation': 1332, 'oh': 1333, 'pink': 5345, "drexel's": 1334, 'uh-huh': 4712, 'toys': 1335, 'punching': 4713, 'royal': 5924, 'fast-paced': 4714, 'dan_gillick:': 1336, 'gift:': 1337, 'walther': 1338, 'load': 4715, 'filth': 4211, 'time': 4716, 'reach': 1340, 'robin': 4717, 'william': 4718, 'friends': 1341, 'mock-up': 1343, 'diet': 3100, "summer's": 4719, 'media': 4720, 'filled': 4721, 'freedom': 4722, 'notorious': 1940, 'mozzarella': 1344, 'ivana': 4723, 'son-of-a': 1345, 'lease': 4214, 'canoodling': 4725, 'mrs': 4726, 'pride': 4727, 'perfect': 4728, 'ziffcorp': 4729, 'believer': 1346, 'sucked': 1347, 'carl': 1065, 'odd': 4730, 'ziff': 6678, 'whatcha': 6468, 'warm_female_voice:': 6258, 'safe': 4655, 'appeals': 4957, 'uneasy': 2852, 'shakes': 1349, 'pleasant': 1350, 'grammar': 1351, 'door': 4731, 'smugglers': 4732, 'perfume': 4733, 'say': 1353, 'according': 1394, "'roids": 1354, 'then': 1355, 'furry': 4736, 'stunned': 4735, 'krusty': 1359, 'species': 4737, 'teriyaki': 4471, 'stay-puft': 1356, 'dead': 4739, 'lone': 4740, 'engraved': 1357, 'kentucky': 3103, 'date': 6472, 'morose': 1358, 'eh': 4741, 'cozies': 3633, 'sweetest': 5928, 'delicious': 1360, 'renders': 4742, 'listen': 1361, 'almost': 5929, 'buddha': 6473, 'blamed': 5347, 'brother': 5742, 'shtick': 4743, 'wars': 4744, 'football': 1362, 'quimby_#2:': 1363, 'radishes': 1364, 'anti-lock': 1365, 'stillwater:': 1366, 'defiantly': 4746, 'how': 1368, 'wait': 1369, 'important': 2687, 'already': 4747, 'mayor': 4748, 'virile': 1370, 'reminds': 4750, 'espn': 1372, "moe's_thoughts:": 4751, 'manager': 1374, 'highball': 4752, 'young_marge:': 4753, 'chew': 4754, 'comment': 4755, 'network': 4784, 'texas': 4756, 'allegiance': 4757, 'lazy': 1375, 'f': 1376, 'where': 1377, 'aww': 4758, 'single': 4759, '||period||': 4760, 'full-bodied': 1378, 'dreamily': 4761, 'world': 4762, 'gives': 4763, 'politics': 5350, 'suppose': 5189, 'elocution': 4765, 'capuchin': 4766, 'cigarette': 4767, "nothin'": 4768, 'faiths': 1380, 'moe-ron': 6089, 'lottery': 1381, 'correct': 4769, 'close': 4770, 'joking': 4771, 'reliable': 4772, 'links': 1384, 'l': 1385, "rasputin's": 1386, 'dad': 4773, 'gargoyle': 4775, 'margarita': 1387, 'yelp': 1388, 'world-class': 4776, 'troy': 1389, 'chateau': 1551, 'guard': 4777, 'glum': 4778, 'kindly': 1390, "guy's": 164, "leavin'": 829, 'told': 1391, 'up-bup-bup': 1392, 'nailed': 1393, 'aisle': 4781, "queen's": 4782, 'thousand-year': 1395, 'damned': 1396, 'sale': 1480, 'exited': 4783, 'robbers': 6528, 'mr': 1397, 'temp': 1398, 'willing': 1399, 'beer-jerks': 1406, 'catty': 1400, 'terrorizing': 4785, 'night-crawlers': 4786, "shan't": 1401, 'novelty': 4787, 'bums': 5936, 'traitor': 1403, 'go-near-': 4788, 'shells': 1404, 'brow': 1405, 'daughter': 4789, 'midge:': 1407, 'sticking-place': 1952, 'sister-in-law': 1408, 'simultaneous': 1409, 'forget': 4791, 'winnings': 4792, 'darn': 4793, 'visas': 6254, 'peeved': 4795, 'sell': 4796, 'prize': 4797, 'coined': 1410, 'bartholomã©:': 4798, 'hey': 4799, "lisa's": 1413, 'happens': 4800, "somethin's": 4801, 'omigod': 4803, 'coins': 4224, 'twenty-five': 1415, 'pinchpenny': 1416, 'wally:': 4804, 'delays': 4805, "'pu": 4806, "y'see": 4807, 'robot': 4035, 'sounded': 4808, '100': 4810, 'raises': 4811, 'tasimeter': 4812, 'crunch': 4813, 'stalin': 4814, 'carl_carlson:': 4815, 'ga': 1420, 'result': 4794, 'spews': 4816, 'rhode': 4817, 'hawking:': 1421, 'musta': 4043, 'gotta': 1422, 'rash': 1666, 'lock': 4819, 'gonna': 1423, 'pas': 1424, 'beast': 1955, 'womb': 1425, 'doors': 4821, "eatin'": 4822, 'clearing': 3642, 'yes': 5357, 'lookalikes': 4228, 'rods': 4823, 'philip': 1426, 'needy': 5435, 'patterns': 4824, 'moment': 4825, 'nascar': 4826, 'quotes': 4827, 'fires': 4828, 'yawns': 4829, 'first': 1429, 'slightly': 2568, 'dressing': 4830, 'chubby': 4831, 'hours': 4832, 'shoulda': 1430, "puttin'": 4833, 'stats': 4834, 'tom': 4836, 'him': 4837, 'egg': 1431, 'mate': 4840, 'diamond': 4839, 'scram': 1433, 'left': 5145, 'chorus:': 4843, 'roller': 1435, 'firm': 1436, 'short_man:': 1438, 'considering:': 1439, 'isotopes': 1440, 'mcstagger': 1441, "crawlin'": 4844, "dimwit's": 1411, "i'm": 4845, "havin'": 1443, 'light': 1444, 'vampire': 1445, 'chumbawamba': 1446, 'pushing': 4847, 'sports_announcer:': 1402, 'comedy': 999, "'n'": 1448, 'streetcorner': 4849, 'screw': 3115, 'refund': 4850, 'holiday': 1450, 'figured': 1451, 'housewife': 1456, "pullin'": 1454, 'squashing': 1455, 'koholic': 5365, 'rid': 4852, 'cologne': 1457, 'an': 1458, 'tony': 1459, 'proper': 1460, 'feisty': 1462, 'methinks': 6346, 'haikus': 4853, 'runners': 4854, 'herself': 4234, 'presently': 4802, 'clincher': 1463, 'process': 4855, 'sad': 1414, 'king': 4856, 'threatening': 1464, 'gasps': 4859, 'afloat': 4860, '2': 1465, 'safely': 4861, 'nominated': 1466, "england's": 4862, 'pian-ee': 4863, 'stories': 1467, 'least': 837, 'rasputin': 4864, 'movement': 1812, 'sue': 1468, 'internet': 4865, 'nudge': 4866, "father's": 1469, 'oh-so-sophisticated': 4867, 'shocked': 1471, 'law-abiding': 1472, 'backbone': 4868, 'mortgage': 4869, 'eventually': 1473, 'newly-published': 1474, 'showered': 1475, 'walking': 1959, 'hooked': 3472, 'mayan': 4870, 'whistles': 1477, 'carolina': 840, 'person': 4871, 'disapproving': 1478, 'marshmallow': 1479, 'ooh': 4872, 'born': 842, "elmo's": 4873, 'awwww': 4874, 'referee': 4875, 'fluoroscope': 1481, "rustlin'": 4876, 'selling': 1482, 'releases': 4877, 'confused': 4878, 'non-losers': 1483, "you've": 4879, 'wedding': 4880, 'developed': 1485, 'kidnaps': 4881, 'loudly': 4882, 'pull': 1486, 'rotten': 1488, 'sink': 1489, 'krusty_the_clown:': 1490, 'mic': 4884, 'spoken': 1491, 'squishee': 4809, 'ice': 1637, 'delivery_man:': 4886, 'conversion': 4887, 'flourish': 4888, 'much': 4889, 'harv': 1964, 'achem': 4890, 'diapers': 1492, 'accelerating': 1493, 'aquafresh': 1494, 'you-need-man': 1495, 'fudd': 4891, 'sheepish': 5235, "mcstagger's": 1496, 'halfway': 1497, 'code': 4892, 'heck': 1498, 'pipe': 1499, 'affectations': 3648, '1895': 1501, 'gunk': 5373, 'sickly': 1503, 'mona_simpson:': 1504, 'blue': 5987, 'jukebox_record:': 2392, 'dã¼ff': 4893, 'kickoff': 4848, 'lately': 1505, 'belts': 1507, 'gr-aargh': 4895, 'using': 4896, 'dictator': 1508, "somethin'": 1509, 'churchy': 1418, 'raking': 4897, 'never': 1968, 'barely': 4898, 'cruiser': 1512, 'tradition': 4899, 'shaggy': 4900, 'bathing': 1513, 'brockelstein': 5952, 'slap': 4901, 'scarf': 4903, 'dying': 3653, 'drop-off': 4904, 'buds': 1515, 'knocks': 4905, 'fat_tony:': 1516, 'actor': 4906, 'uncreeped-out': 4907, 'sky': 4908, 'tubman': 4909, 'nice': 1519, 'whup': 3655, 'exception:': 4910, 'star': 1521, 'gun': 1971, 'wagering': 4912, "i'll": 4913, 'crossed': 5376, 'skins': 4914, 'sprawl': 4915, '50%': 4916, 'that': 4917, 'teddy': 1523, 'dizer': 4918, 'forget-me-drinks': 4919, 'fan': 4920, 'refinanced': 4921, "fallin'": 1525, 'corkscrews': 1526, 'mail': 4923, 'whoa-ho': 1528, 'gees': 5583, 'eighty-one': 4924, 'cushion': 4925, 'discriminate': 1530, 'cyrano': 4926, 'starters': 4928, 'cocoa': 4929, 'everybody': 4930, 'brought': 4931, 'honored': 4932, "readin'": 2582, 'statesmanlike': 1532, 'lugs': 4933, 'lofty': 4724, 'seminar': 849, 'sniffs': 4934, 'sick': 1534, 'so-ng': 1535, 'desperately': 4935, 'instantly': 4936, 'heart-broken': 4937, 'americans': 4938, 'eighty-seven': 850, 'squirrels': 4939, 'causes': 6493, 'percent': 3514, '3': 4820, 'sass': 1537, 'compliments': 1538, 'near': 4940, 'rush': 1539, "murphy's": 4941, 'hostages': 4942, 'tank': 1540, "year's": 4943, 'covers': 4944, 'decide': 4945, 'dateline:': 4946, 'churchill': 4947, 'bret:': 852, 'turns': 1541, 'grateful': 4949, 'cursed': 4950, 'remembers': 4951, 'other_player:': 1542, 'mop': 1543, 'crowd:': 4952, 'chained': 1545, 'literary': 1548, 'massage': 4953, 'barney-shaped_form:': 5956, 'trolls': 5733, 'incredulous': 4954, 'ã': 4955, 'riveting': 717, 'sweetie': 4956, 'know': 1550, 'microphone': 4958, 'colossal': 1552, 'screws': 1553, 'vengeance': 1554, 'gator': 1555, 'pats': 1556, 'again': 4959, 'fortress': 1557, 'unsanitary': 4960, 'navy': 5415, 'bike': 4961, 'hostile': 4962, 'publishers': 4963, 'burp': 4964, 'pickles': 1558, 'irish': 4249, 'fans': 5315, 'bras': 3664, 'heh-heh': 4965, 'creme': 1559, 'dennis_conroy:': 1560, 'mamma': 1561, 'driver': 4966, 'tactful': 1563, 'sobriety': 1564, "'cause": 1565, 'giant': 5791, 'so-called': 4968, 'conditioners': 6774, 'technical': 1566, 'enhance': 3665, '||question_mark||': 1427, 'groan': 4969, 'scientific': 1568, 'fit': 1569, 'trash': 4970, "challengin'": 4971, 'cries': 6086, 'oblongata': 4972, 'begins': 1570, 'david': 4975, 'clear': 4976, 'bar-boy': 4980, "aristotle's": 1428, 'girl-bart': 4981, 'tee': 4982, 'tester': 1572, 'shark': 6500, 'grocery': 4984, 'admitting': 4985, 'syndicate': 856, 'dealie': 1573, 'mis-statement': 1574, 'hispanic_crowd:': 1575, 'ass': 1576, 'despite': 4986, 'jimmy': 3138, 'limited': 1578, 'fwooof': 4987, 'female_inspector:': 4988, 'large': 3133, 'suds': 1579, 'crooks': 3136, 'jackson': 1580, "table's": 5262, 'dropping': 1583, '_hooper:': 3137, 'renew': 4989, "beer's": 4990, 'noggin': 6088, 'happiness': 1584, 'lime': 3634, 'captain': 4991, 'hanging': 3734, 'knowing': 3669, 'old': 1586, "this'll": 3859, 'thirsty': 1587, 'hiring': 1588, 'comeback': 4992, 'thirteen': 4993, 'capitol': 4994, 'delighted': 5384, 'hemoglobin': 1590, 'gus': 5520, 'examines': 1591, 'presidential': 4996, 'kept': 1592, 'conversation': 4997, 'equivalent': 4835, 'apulina': 4998, 'thrown': 4999, "o'reilly": 2597, 'yesterday': 6509, 'boy': 1594, 'neighboreeno': 1596, 'railroads': 1597, 'youngsters': 3512, 'intimacy': 1598, 'maher': 5001, 'means': 5002, 'mailbox': 1991, 'germany': 5003, 'curds': 1601, 'luv': 1602, 'adopted': 1604, 'corn': 1606, 'carl:': 4841, 'effervescent': 2547, 'regulars': 5005, 'brothers': 6231, 'civic': 1607, 'ashamed': 1608, 'revenge': 5006, 'smoke': 6013, 'brine': 5213, 'executive': 1609, 'flips': 5007, 'grubby': 5009, 'thirty-five': 1437, '||left_paren||': 1613, "weren't": 2720, 'moron': 5010, 'control': 3675, 'sorts': 5011, 'indeed': 5012, 'ignorance': 5957, 'honest': 6510, "cleanin'": 5013, 'sense': 5014, 'eu': 1616, 'today/': 1617, 'knock': 5876, 'commit': 5015, 'liar': 1618, 'normal': 5016, 'veteran': 1619, 'al_gore:': 5017, 'ralph': 1621, 'ummmmmmmmm': 1622, 'geysir': 5023, 'steamed': 5021, 'swine': 5022, 'awkward': 5025, 'pin': 1623, 'locked': 1624, 'flea:': 5026, 'aiden': 3678, 'cocktail': 5028, 'skills': 1627, 'small_boy:': 1442, 'center': 1628, 'top': 5029, 'else': 5030, 'jebediah': 1630, 'cab': 1631, 'should': 1632, "grandmother's": 5031, 'disgusted': 5032, 'crow': 5766, 'astronaut': 2600, 'points': 5033, "robbin'": 1633, 'plastic': 5034, 'lainie:': 5036, 'social': 4846, 'rage': 5037, 'packets': 5038, 'lewis': 5039, 'valuable': 1634, 'wanna': 5040, 'ebullient': 5041, 'neil_gaiman:': 4790, 'move': 5392, 'hydrant': 1635, 'parents': 5042, 'bulldozing': 561, 'shades': 5962, 'order': 1636, 'laney_fontaine:': 5044, 'myself': 1638, 'could': 1639, "marge's": 5045, 'mother': 1640, 'offensive': 1641, 'intelligent': 1642, 'germs': 5046, 'whoopi': 5048, 'amends': 5049, 'quality': 1643, 'fill': 5050, 'boozer': 5051, 'most': 2539, 'x': 5304, "game's": 5052, 'generosity': 5053, 'ahhh': 1644, 'punches': 5055, 'chunk': 5056, 'champs': 5057, 'cheers': 5058, 'stretch': 5397, 'falsetto': 5060, 'cronies': 1447, 'calvin': 1645, 'castle': 5062, "linin'": 5063, 'british': 1646, 'be': 5064, 'testing': 1449, 'jack_larson:': 5065, 'culkin': 5066, 'life-threatening': 1647, 'wowww': 1648, 'loathe': 5067, 'size': 1652, 'situation': 4673, "spaghetti-o's": 1653, 'index': 1655, 'clears': 5068, '1979': 5069, 'source': 4851, 'whispered': 5070, 'insensitive': 5071, 'james': 5072, 'hurting': 1657, 'prizefighters': 5073, 'deep': 1658, "duff's": 4582, 'merchants': 2525, 'frink': 5074, 'koi': 1659, 'lend': 5075, "sat's": 1660, 'overturned': 5076, 'pile': 5077, 'doof': 1452, 'carll': 1662, 'expired': 1663, 'trapping': 5078, 'jerry': 3373, "she'll": 5754, 'prayer': 1665, 'bunch': 5079, 'fortensky': 5080, 'smithers': 332, "tellin'": 4270, 'arab_man:': 5081, 'fly': 6200, 'half': 1669, 'wistful': 1670, 'sooner': 5082, 'passes': 5083, 'panicked': 1672, 'birthplace': 6386, 'shack': 1674, 'homer': 1675, 'eye': 5084, 'or': 1676, 'marquee': 5085, 'idiot': 5086, 'afterglow': 1678, 'pledge': 5087, 'hoo': 1679, 'u': 5088, 'riding': 1680, 'weeks': 3984, 'blobbo': 5969, 'finding': 1681, 'rhyme': 1382, 'marched': 1461, 'naegle': 5090, 'nigeria': 5091, 'musses': 51, 'lucius:': 1682, 'jaegermeister': 5092, 'early': 1683, 'charges': 5093, 'parking': 1684, "countin'": 1685, 'griffith': 1686, 'sponge:': 1687, 'love-matic': 1688, 'tying': 6518, 'manipulation': 1690, 'malted': 5094, 'conference': 1691, 'farewell': 6519, 'fire': 6762, 'chow': 5095, 'rain': 3155, 'gil_gunderson:': 5096, "coffee'll": 1692, 'society': 3447, 'improved': 1693, 'shoulders': 1694, 'assert': 5097, 'possessions': 5098, "could've": 1107, 'hanh': 1697, 'burglary': 1698, 'simpson': 1700, 'jumping': 2613, 'hippies': 1703, 'pond': 5099, 'pleased': 5100, 'refreshing': 5973, 'negative': 5102, 'chapter': 3688, 'understand': 5103, "squeezin'": 5104, 'bedbugs': 4161, 'weep': 1704, 'wasting': 5105, 'jerky': 4858, 'address': 5106, 'france': 4857, 'theatah': 1706, 'schemes': 5107, 'scoffs': 1707, 'show': 1709, 'linda_ronstadt:': 5108, 'picnic': 1710, 'bumbling': 1711, 'wakede': 1712, 'share': 4304, 'frozen': 1713, 'flash-fry': 5109, 'waist': 5110, 'waylon_smithers:': 5111, 'awkwardly': 5112, 'neighborhood': 5113, 'radio': 5114, 'sheriff': 1716, 'kadlubowski': 5115, 'celebrities': 5116, "president's": 1717, 'bachelor': 1718, 'scent': 1719, 'played': 6524, "ridin'": 1720, 'whoo': 5117, 'roz:': 1721, 'higher': 1722, 'cost': 5118, 'widow': 1723, 'disturbing': 5120, 'straighten': 5943, 'citizens': 5121, 'changes': 5126, 'joint': 5123, 'flophouse': 290, 'whoever': 5125, 'group': 1724, 'mid-conversation': 1725, "c'mere": 5128, 'mike': 6199, 'victory': 1726, 'perã³n': 5129, 'through': 1727, 'alky': 6593, 'omit': 1728, 'disgraceful': 5130, 'crisis': 5131, 'survive': 4281, 'land': 5132, 'thoughts': 5133, 'elder': 1729, "how're": 1730, 'zone': 5135, 'mccall': 5136, 'finance': 1731, 'imported-sounding': 5137, 'become': 5138, 'lady_duff:': 5004, 'can': 1732, 'hang': 5139, 'ad': 5407, 'pointy': 5140, 'hose': 5141, 'picked': 1733, 'fine': 5144, 'hottest': 1734, 'tobacky': 1735, 'drunks': 1736, 'seem': 1737, 'denser': 5147, 'followed': 5148, 'raising': 1738, 'vigilante': 1739, 'puzzle': 1740, 'soft': 3697, 'male_singers:': 5150, 'ladies': 1741, 'grampa_simpson:': 5151, 'design': 6335, 'noise': 5874, 'arise': 5153, 'night': 5154, "can't": 6531, 'lloyd:': 1742, 'joy': 1743, 'hunky': 5157, 'twelve': 5158, 'snackie': 1744, 'disgrace': 6050, 'cheerleaders:': 1745, "where's": 1746, 'boys': 1747, 'accusing': 5160, "jimbo's_dad:": 5161, 'advice': 210, 'gosh': 1750, 'raining': 1752, 'since': 1753, 'rumaki': 5162, 'boo': 5163, 'disappeared': 1755, 'easily': 5165, 'soaking': 1756, 'soap': 5166, 'ironic': 1757, 'diving': 5167, 'sharing': 5168, 'future': 5981, 'opportunity': 1758, 'appointment': 5169, 'grow': 1759, 'family-owned': 5170, 'mccarthy': 1760, 'crank': 1761, 'ab': 5473, 'experience': 5171, 'academy': 1762, 'evening': 5172, 'thomas': 5173, 'site': 5174, 'card': 1764, 'way': 1765, 'anniversary': 1766, 'art': 1767, 'ayyy': 1768, 'black': 1769, "football's": 1770, 'faint': 5175, 'host': 1771, 'smells': 5177, 'alpha-crow': 5179, 'craphole': 1772, 'nucular': 6534, 'washouts': 1773, 'misconstrue': 5181, 'tail': 3703, 'what-for': 1774, 'tolerable': 5182, 'sold': 5183, 'flower': 1775, 'worth': 5413, 'creates': 1776, 'rap': 1777, 'whaddya': 5185, 'wrap': 3705, 'prints': 1778, 'natured': 5186, 'mel': 5416, 'sector': 1779, "edna's": 1780, 'associate': 5188, 'bees': 5190, 'cupid': 5191, 'burg': 5192, 'surprised/thrilled': 5194, 'unearth': 2471, 'sketch': 5195, 'gifts': 5839, 'kids': 1782, 'faulkner': 1783, 'sleeping': 1785, 'confession': 5968, 'gums': 1786, 'breathtaking': 5199, 'elect': 5200, 'enjoy': 295, 'ambrosia': 1787, 'man': 5201, 'harm': 3709, 'monday': 5202, 'easygoing': 1788, 'chinese': 5204, 'blinded': 6665, 'anguished': 1789, 'enforced': 1790, 'refiero': 5205, 'barbara': 5206, 'limericks': 1476, 'brick': 297, 'illustrates': 1791, 'sponge': 299, 'bedridden': 5207, 'medicine': 1793, 'website': 5208, 'edner': 5209, 'housework': 878, 'gel': 1794, "bladder's": 1795, 'presto:': 2030, 'goes': 1796, 'plow': 1797, "renee's": 5210, 'best': 5211, 'public': 1798, 'famous': 1799, 'hooch': 6544, 'augustus': 5214, 'coming': 1800, 'drawer': 1801, 'subscriptions': 1802, 'effervescence': 5215, 'she': 5216, 'floated': 1803, 'dna': 1804, 'heartily': 5217, 'casual': 5218, 'sisters': 5219, 'attention': 5220, 'haircuts': 3169, 'bookie': 1807, 'immiggants': 5419, 'talk-sings': 1234, 'rough': 1808, "children's": 350, 'at': 5221, 'unison': 5222, 'detail': 5223, 'reaching': 1809, 'woozy': 1810, 'west': 1811, 'bliss': 1813, 'ha': 5226, 'civil': 1814, 'fat': 5227, 'investigating': 1815, 'wine': 2036, 'interrupting': 1816, "takin'": 5229, 'flat': 3172, "bo's": 1817, 'sanitation': 1818, 'other_book_club_member:': 1819, 'unjustly': 1820, 'applicant': 1821, 'cliff': 1822, 'paying': 1823, 'bugging': 4295, 'yo': 1824, 'day': 1825, 'twerpy': 1826, 'occupation': 1827, 'boxing_announcer:': 1829, 'short': 5236, 'jerking': 5966, 'displeased': 6115, 'realizing': 1830, 'celebrate': 6742, 'relaxing': 1831, 'iran': 1832, 'glitterati': 5239, 'picky': 5939, 'pointless': 5240, 'savagely': 302, 'justify': 1833, 'cheaped': 1834, "stabbin'": 1835, 'unlike': 885, 'title:': 1836, 'involved': 1837, 'relieved': 1838, 'resenting': 1839, 'tabooger': 1840, 'waste': 5244, 'dunno': 5246, 'unavailable': 1841, 'jay': 5248, 'gluten': 1842, 'exciting': 5250, 'kent_brockman:': 1843, 'smart': 5251, 'country-fried': 1844, 'yak': 5993, 'stinky': 5252, 'question': 6552, "others'": 1845, 'cigarettes': 5253, 'reckless': 1847, "what'll": 1848, 'hmm': 1849, 'tasty': 5254, 'hopeful': 1484, 'machine': 5255, 'seemed': 1850, 'mind-numbing': 5256, 'szyslak': 889, 'john': 5258, 'shop': 1851, 'archaeologist': 1852, 'slight': 5261, 'tip': 1854, 'resist': 1855, "ain't": 5263, 'porn': 5264, 'certain': 5265, 'shoulder': 1856, 'cards': 5266, 'butt': 1857, 'very': 1858, 'hare-brained': 5268, 'boxer': 5269, 'language': 1860, 'spamming': 5270, 'what': 5431, 'aer': 5271, 'dig': 1861, 'fears': 5272, "smackin'": 1862, 'sabermetrics': 5273, 'bindle': 3725, 'carey': 892, 'i-i-i': 6554, 'gesture': 5275, 'cowboy': 5995, 'restless': 4883, 'retired': 3727, 'oddest': 5276, 'smoothly': 5277, 'thnord': 1864, 'interesting': 1865, 'plums': 1866, 'mouth': 5278, 'ahhhh': 5826, 'eyes': 1867, 'besides': 5279, 'prep': 309, '91': 1868, 'nfl_narrator:': 5281, 'legally': 1869, 'flanders:': 5282, 'disgracefully': 5284, 'once': 5285, 'i-i': 1870, 'hoping': 5286, 'raise': 5287, 'stomach': 1871, 'biggest': 5288, "'s": 1872, 'ticket': 5289, 'combine': 5290, 'stupidest': 1873, 'stink': 1874, 'singing/pushing': 1875, 'character': 5291, 'lowers': 1876, "what's": 5998, 'gary_chalmers:': 1877, 'tinkle': 1878, 'mom': 1879, 'manjula_nahasapeemapetilon:': 5292, 'brightening': 5293, 'kissingher': 5295, 'blues': 5296, 'frink-y': 6578, 'end': 1880, 'watt': 1881, 'stripes': 1882, 'dude': 5297, 'tickets': 3736, 'gotten': 1884, 'suck': 1885, 'blend': 1886, 'y': 5019, 'blubberino': 5300, 'blokes': 4305, 'monkeyshines': 1887, 'misfire': 5301, 'speed': 6116, 'enthusiasm': 5302, 'whee': 6557, "wallet's": 1888, 'agnes_skinner:': 1889, 'device': 1890, 'ashtray': 5303, 'tell': 5305, 'heartless': 5306, 'angry': 1892, 'really': 1893, 'on': 1894, 'thirty-nine': 1895, 'connor': 898, 'stools': 1896, 'manfred': 1897, 'a-a-b-b-a': 1898, 'health': 1899, 'lotta': 5999, 'homie': 3892, 'carlson': 4306, 'stamps': 5309, 'military': 1900, 'teams': 5312, 'wow': 1901, 'hot': 1902, 'break-up': 1903, 'gulliver_dark:': 1114, "b-52's:": 5313, 'balloon': 5314, 'restroom': 1904, 'shareholder': 1905, 'tribute': 1906, 'duff': 1907, 'sob': 2648, 'stores': 5317, 'wishful': 1908, 'six-barrel': 1909, 'superior': 5318, 'taylor': 1910, 'set': 1911, 'plane': 5320, 'si-lent': 1912, 'proposing': 1913, 'offense': 1914, 'christopher': 5322, "people's": 1915, 'assume': 1547, 'eyeballs': 1142, 'perhaps': 2054, 'limits': 5324, 'heard': 1916, 'entirely': 1917, 'illegally': 6005, 'pickle': 5325, 'hmmm': 1919, 'dungeon': 1921, 'help': 1922, 'puke-holes': 5328, 'micronesian': 5442, 'more': 5329, 'pardon': 1923, 'jubilant': 5330, 'simp-sonnnn': 2056, 'minister': 5331, 'indignant': 1925, "liberty's": 6560, 'gunter:': 5332, 'remaining': 2058, 'plywood': 1926, 'recorder': 1927, 'glad': 6561, "coaster's": 3184, 'edge': 1928, 'present': 1929, 'amber_dempsey:': 5334, 'na': 1930, 'endorse': 1931, 'theory': 4309, 'whining': 1500, 'impress': 5336, 'sixty': 5337, 'pre-game': 1932, '1973': 5338, 'completely': 5339, 'harder': 1933, 'changing': 1934, 'bonfire': 5341, 'singing': 1935, 'confident': 313, 'divorced': 3945, 'arrange': 5342, 'artie': 5343, 'society_matron:': 1936, 'reactions': 1937, 'puts': 1938, 'sneeze': 1939, 'meatpies': 1942, 'plan': 5346, 'push': 1943, 'train': 1944, 'naval': 1502, 'let': 1945, 'pussycat': 1946, 'cash': 5348, 'quit': 1947, 'parked': 5349, 'volunteer': 6569, 'moe-lennium': 1948, 'winston': 5351, "meanin'": 1949, 'startled': 5352, 'authenticity': 1950, 'travel': 1951, 'barber': 315, 'offended': 5353, 'court': 2651, 'taxi': 1953, 'painted': 1954, 'chinua': 5355, 'thankful': 5356, 'sacrilicious': 5358, 'outrageous': 1956, 'hat': 5359, 'rolls': 1957, 'wang': 5360, 'ear': 5361, 'foil': 3187, 'cut': 5362, "drinkin'": 5363, 'ing': 5364, "doin'": 1958, 'lover': 1487, 'impatient': 5451, 'self-esteem': 5366, 'smiling': 3746, 'doooown': 1960, 'yell': 5367, 'terror': 5368, 'all': 5369, 'innocuous': 5370, 'team': 1962, 'burn': 1963, 'tender': 5371, 'gave': 5994, 'literature': 5372, 'industry': 1965, 'chug-a-lug': 1966, 'flaking': 4315, 'snotty': 1967, 'oooo': 6475, 'tracks': 5374, 'lis': 6321, 'dollface': 1969, 'local': 1970, 'thunder': 3192, "maggie's": 1972, 'down': 5377, 'idiots': 5378, 'linda': 1973, 'seat': 1286, 'dyspeptic': 2067, 'unusually': 6016, 'drinks': 5379, 'veux': 1974, 'badges': 1975, 'hawaii': 5380, 'recreate': 1976, 'lucinda': 6579, 'michael_stipe:': 1977, 'just': 1978, 'store-bought': 1979, "wouldn't-a": 1980, 'tar-paper': 5381, 'motor': 1981, 'curse': 5382, 'town': 5383, "'tis": 5452, 'felony': 1982, 'bully': 1983, "today's": 1985, 'burt': 1986, 'dismissive': 5385, 'feminist': 4894, 'fox_mulder:': 1987, 'ads': 1988, 'salvation': 1989, "monroe's": 1993, 'startup': 1992, 'against': 4320, 'super': 1994, 'dreamed': 5386, 'times': 5387, 'save': 5388, 'pregnancy': 5389, 'bubbles': 1995, 'aims': 1996, 'quiet': 1997, "everyone's": 1998, 'hearing': 5391, 'bottoms': 1348, 'spending': 1999, 'brandy': 2000, 'our': 2001, 'bee': 2002, 'powers': 2003, 'journey': 5393, 'synthesize': 916, 'slow': 5394, "who'll": 5395, 'lisa': 5396, 'booking': 2004, 'repeating': 6020, 'his': 2005, 'champignons': 2006, 'toasting': 2007, 'tanked-up': 2008, 'shard': 5398, 'fbi': 2009, 'nation': 2010, 'eyesore': 2011, 'beverage': 5399, 'helllp': 2012, 'quebec': 2013, 'it': 5400, 'progress': 1510, 'rich': 5401, 'outside': 6584, 'andrew': 321, 'wall': 2014, 'cherry': 5403, 'seeing': 6585, 'dryer': 5455, 'accidents': 1511, 'zinged': 5404, 'cheaper': 5405, 'poplar': 2015, 'options': 2016, 'pugilist': 5406, 'pulitzer': 2017, 'cheerier': 4326, 'ratio': 2018, '/': 5888, 'w-a-3-q-i-zed': 5408, 'dã¼ffenbraus': 5409, 'ah-ha': 5410, 'studied': 5466, 'alec_baldwin:': 2019, "feelin's": 2020, 'law': 5411, 'smokes': 2021, 'edna_krabappel-flanders:': 2022, 'risquã©': 2023, 'cigars': 2024, 'glowers': 2025, 'kool': 2026, 'shesh': 5417, 'feminine': 2027, 'indifference': 6587, 'dilemma': 6023, 'stupid': 2028, "aren't": 2029, "springfield's": 5418, 'lucky': 2031, 'faith': 2032, 'japanese': 1069, 'theater': 2033, 'side': 2034, 'fabulous': 180, 'names': 5420, 'victim': 5421, "who'da": 5422, 'staying': 2037, 'though:': 5423, 'wolfe': 2039, 'polishing': 2040, 'tie': 2041, 'virtual': 2042, '50-60': 5424, 'rugged': 5425, 'careful': 5426, 'white_rabbit:': 6025, 'nectar': 5427, 'coal': 2043, 'clenched': 4334, 'lou': 5428, 'bloodiest': 5429, 'works': 2044, 'hot-rod': 2045, 'faded': 6028, 'dentist': 2046, 'beached': 5461, 'huh': 5432, 'labor': 5433, 'der': 5434, 'trouble': 3205, 'knew': 2047, 'shelf': 6029, 'haiti': 2048, 'fraud': 5436, 'wash': 2667, 'rom': 1514, 'walther_hotenhoffer:': 5438, 'huggenkiss': 5439, 'application': 2049, 'camp': 893, 'wipe': 5440, 'forty-five': 2050, 'truck': 2052, 'label': 2053, "should've": 5441, 'grim': 5443, 'bible': 2055, 'whether': 5444, 'blinds': 5445, 'confidence': 2057, 'fdic': 5446, 'man_with_tree_hat:': 2059, 'appearance-altering': 5447, 'horses': 2060, 'joke': 6033, 'initially': 2061, "drivin'": 5448, 'put': 5449, 'shutup': 2062, 'whip': 2063, 'starving': 5450, 'scooter': 2064, 'guide': 2065, 'hello': 2066, 'nash': 2068, 'glummy': 2069, 'wiggle': 2070, 'baritone': 2071, 'gently': 4337, 'delightful': 2072, "pope's": 2073, 'people': 2075, 'obama': 3762, 'plastered': 5453, 'wooooo': 5454, 'explanation': 925, 'banned': 1518, 'selma_bouvier:': 2076, 'slaves': 3444, 'motto': 5456, 'minus': 2077, 'latour': 4339, 'start': 5457, 'richard:': 5460, 'highway': 2078, 'maybe': 5462, "listenin'": 2079, 'dishrag': 5464, 'boozy': 5465, 'nelson': 2080, 'supports': 2081, 'patty_bouvier:': 5468, 'hooky': 5469, 'crony': 2083, 'fix': 1520, 'stadium': 2051, 'blood-thirsty': 5470, 'pharmaceutical': 2085, 'luxury': 2086, 'view': 5472, 'sacajawea': 2087, 'attack': 5935, 'kegs': 2088, 'whoops': 2089, 'albert': 5474, 'lanes': 4911, 'hates': 5475, 'morning': 5476, 'vanities': 2090, 'luckily': 2091, 'fanciest': 5484, 'same': 5471, "lovers'": 2092, 'ruined': 5478, 'runaway': 2093, 'own': 1649, 'giggle': 2094, 'notice': 2097, 'attractive_woman_#1:': 2096, 'handshake': 5480, 'bad-mouth': 2098, 'learned': 5481, 'agreement': 2099, 'leave': 2101, 'stuck': 2102, 'failure': 5483, 'thousand': 2103, 'amazed': 2104, 'multiple': 2105, 'guessing': 5485, 'figures': 2106, 'sap': 5486, 'outta': 2107, 'muslim': 5487, 'dumbass': 5488, 'indeedy': 2108, 'shame': 5489, 'suits': 2109, 'this': 5490, 'shares': 2110, 'donuts': 5492, 'bulked': 2111, "calf's": 2112, 'hilarious': 2113, 'dull': 5493, 'anti-intellectualism': 2115, 'type': 2116, 'bumblebee_man:': 2117, 'wrestling': 2118, 'heads': 5495, 'child': 2120, 'twelve-step': 2121, 'truth': 2122, 'milhouses': 6598, 'caveman': 5498, 'scruffy_blogger:': 5499, 'kirk_van_houten:': 333, 'hide': 5502, 'replaced': 2123, 'windelle': 2124, 'queen': 1524, 'breakfast': 335, 'tomato': 5505, 'fills': 932, 'today': 2125, 'baseball': 2126, 'challenge': 2127, 'selection': 2128, 'experiments': 5477, 'cooker': 2129, 'quick': 5507, 'ferry': 2130, 'prank': 5508, 'toss': 3770, 'cueball': 5509, 'swatch': 2131, 'bill': 5510, 'chug-monkeys': 2132, 'tsking': 5511, 'vacuum': 5512, 'seconds': 933, 'bill_james:': 2133, 'dutch': 2134, 'aw': 5513, 'electronic': 5514, 'charter': 2135, "dad's": 4350, 'whim': 2137, 'cockroaches': 2138, 'writing': 2139, 'ingredient': 5515, 'onion': 2140, 'butterball': 2143, 'swishkabobs': 5516, 'photos': 2144, "bringin'": 5517, 'terrace': 5518, 'sleep': 1585, 'mexican': 2145, 'extended': 2146, 'sugar': 2147, 'hub': 2148, 'cold': 2150, 'kennedy': 935, 'mabel': 2151, 'crap': 2152, 'column': 2154, 'collapse': 2155, 'appear': 5522, "homer'll": 342, 'bites': 2159, 'death': 2158, 'mural': 5524, 'forever': 343, 'across': 5525, 'lurleen_lumpkin:': 5526, 'gruff': 4927, 'kissing': 5479, 'stewart': 3220, 'sunday': 2160, 'temporarily': 2161, 'snort': 2162, 'insurance': 2163, 'jackpot-thief': 2164, 'invite': 6558, 'bono': 2165, 'highest': 2166, 'grain': 6602, 'latin': 2167, 'attractive': 6044, 'inanely': 2168, 'occupied': 2169, 'mushy': 5530, 'paid': 2170, 'jump': 5531, 'tv_daughter:': 5532, 'wh': 5533, 'yet': 2095, 'when': 5534, 'quarterback': 2171, 'bulletin': 2173, "don't": 2174, 'program': 1531, 'cares': 5535, 'tax': 5536, 'oblivious': 3777, 'shrieks': 5538, 'geez': 5539, 'sidelines': 2175, 'give': 938, 'laid': 5540, "that'd": 2176, 'george': 5542, "changin'": 2177, 'dang': 2178, 'wallet': 940, 'orifice': 5544, 'beyond': 5545, 'temples': 5546, 'sinkhole': 1246, "high-falutin'": 2179, 'fontaine': 2180, 'reasons': 941, 'hateful': 5547, 'thoughtful': 6605, 'gunter': 2181, 'indecipherable': 2182, 'nose': 5549, 'crack': 5550, 'homer_simpson:': 943, 'badge': 5942, 'businessman_#2:': 2183, 'landfill': 2184, 'carney': 2185, 'seymour': 4365, 'attracted': 1533, 'frankenstein': 2186, 'draw': 5551, 'elizabeth': 2187, 'regulations': 2100, 'junkyard': 5552, 'swan': 3227, 'playing': 2188, 'barkeep': 2189, 'watered': 5553, 'turning': 5482, 'curious': 4367, 'stirrers': 6608, 'keys': 1367, 'jar': 5555, 'popped': 5556, 'toxins': 5557, 'broom': 5558, 'all:': 5559, 'pantry': 5562, 'candy': 2190, 'thanking': 2191, 'schorr': 5563, "that'll": 5564, 'brunch': 5565, 'spit-backs': 2192, 'published': 5566, 'sea': 5567, 'ech': 2193, 'four-star': 2194, "man's": 2195, 'sniffles': 2196, 'mull': 2197, 'love': 2198, 'sorry': 2199, 'creature': 2084, 'eat': 2200, 'serve': 5570, 'jailbird': 2201, 'matter-of-fact': 2202, 'rule': 2203, 'discussing': 2204, 'burger': 5571, 'recent': 2205, 'expression': 2206, 'annus': 5572, 'typed': 2207, 'expect': 5573, 'solves': 2209, 'fica': 5575, 'rig': 6610, 'fat_in_the_hat:': 2210, 'adjourned': 5576, 'looked': 5577, "seein'": 2211, 'alfalfa': 2212, "i've": 2213, 'spiritual': 5579, 'wok': 5580, 'diaper': 5581, 'fistiana': 5584, 'disaster': 2214, 'certificate': 2215, 'meet': 2216, 'surprising': 2218, 'science': 2220, 'continued': 353, "nothin's": 946, 'waking-up': 6612, "what'd": 5586, 'quite': 2222, 'sealed': 2223, 'othello': 5588, 'tenor:': 5589, 'call': 2224, 'mystery': 947, 'ma': 2225, 'wieners': 5591, 'lap': 2226, 'stingy': 5592, 'macbeth': 2227, 'mary': 2228, 'fools': 2229, 'bears': 2230, 'suddenly': 5594, 'extremely': 6054, 'desire': 5595, 'grabbing': 2231, 'lee': 2232, 'based': 5596, 'supervising': 4376, 'breath': 2829, 'voyager': 2233, 'praise': 2235, 'mess': 5600, 'fainted': 5602, 'braun:': 5603, 'hangout': 5604, 'house': 2236, 'childless': 2237, 'ointment': 358, 'planted': 676, 'laughs': 6614, 'feld': 5609, 'utensils': 5610, "payin'": 3242, 'tow': 2238, 'oil': 2239, 'plain': 2240, "they'll": 2241, 'snitch': 2242, 'jam': 5611, 'england': 356, 'simplest': 2243, 'doll-baby': 5613, 'defensive': 4378, 'sistine': 6615, "homer's_brain:": 5614, 'adrift': 2245, 'babe': 5615, 'treats': 2114, "hobo's": 2248, 'little_man:': 5616, 'cousin': 2249, 'sympathy': 2250, 'detective_homer_simpson:': 2251, 'ourselves': 2252, 'reading': 2253, 'imagine': 5617, 'emporium': 5618, 'ninth': 2254, 'cakes': 2255, 'reached': 6057, 'case': 2257, "s'okay": 2260, 'kirk': 1522, 'alive': 5620, "collector's": 362, "soakin's": 2261, 'smell': 2262, 'television': 2263, 'wolverines': 2264, 'weird': 365, 'betrayed': 2265, 'bachelorette': 2119, 'practically': 5621, 'padre': 5622, 'pair': 3786, 'president': 2267, "enjoyin'": 2268, 'bathroom': 2269, 'in-ground': 5625, 'fondly': 5494, 'flack': 3739, 'radiator': 2270, 'decide:': 5626, 'buttocks': 5627, 'perfunctory': 2271, 'splash': 2272, 'sympathetic': 5628, 'jewish': 5629, 'burns': 5142, 'recap:': 2273, 'contractors': 2274, 'nasty': 2276, 'stooges': 2277, 'salary': 2278, 'holds': 2279, 'clubs': 952, 'ohh': 5497, 'excellent': 5632, 'brag': 2280, 'raging': 5633, 'waters': 2281, 'tapestry': 5491, 'attempting': 5635, "getting'": 4948, 'jumps': 2282, 'sports': 2283, 'gutenberg': 5637, 'hear': 2285, 'horns': 3790, 'fonzie': 5638, 'wiggum': 4382, 'adult': 2286, "battin'": 2287, 'poetry': 2288, 'pepper': 5640, 'nobel': 2289, 'hour': 2290, 'charlie:': 2292, 'cameras': 3248, 'prejudice': 2293, 'writer:': 5642, 'unattractive': 5501, 'refreshment': 2294, 'snake_jailbird:': 5643, 'position': 2295, 'delightfully': 2296, 'spied': 5645, 'donated': 5646, 'habitrail': 5504, 'ripper': 2297, 'nor': 4383, 'extinguishers': 2298, 'supply': 2299, 'aid': 2301, 'cheered': 2302, 'protesting': 5648, 'detecting': 2303, 'party': 2304, 'admiration': 6621, 'furniture': 369, 'dirge-like': 2305, 'insecure': 2306, "brockman's": 1544, 'nicer': 2307, 'return': 2308, 'pretentious_rat_lover:': 2309, 'does': 5652, 'focused': 2310, 'swig': 2311, 'beer': 2312, 'jury': 958, 'cleaning': 5820, 'blocked': 2315, 'stu': 3798, 'drunk': 2317, 'gumbo': 1258, 'spirit': 2318, "somebody's": 2320, 'mistresses': 3795, 'plaintive': 5655, 'ago': 5656, 'ominous': 5658, 'federal': 2321, 'interested': 980, 'telemarketing': 5659, 'tourist': 1546, 'pig': 5661, 'stern': 2322, "tootin'": 2323, 'cartoons': 5662, 'sponsoring': 5663, 'room': 5664, 'spot': 5665, 'fictional': 2324, 'cajun': 5506, 'bags': 2325, 'vermont': 5666, 'salvador': 2327, 'lift': 3801, 'gig': 2328, 'triumphantly': 5668, 'calm': 2329, 'answered': 2332, "workin'": 2331, 'window': 5670, 'enabling': 5671, 'depending': 5672, 'th-th-th-the': 2334, 'class': 5673, 'beer-dorf': 5674, 'yello': 2335, 'floating': 2336, 'past': 5677, 'jay:': 5679, 'perverse': 6394, 'dynamite': 5680, 'nope': 2337, "mo'": 5681, 'ends': 966, 'book': 2338, 'meaningfully': 5682, 'harrowing': 2339, 'sign': 5684, 'change': 2340, 'lots': 2341, 'gum': 5612, 'there': 2342, 'kitchen': 2035, 'priest': 2343, 'liser': 2344, 'larry': 5689, 'promotion': 2345, 'car:': 3744, 'bushes': 2346, 'twentieth': 2347, 'washed': 5690, "here's": 2348, 'sideshow': 5691, 'weapon': 1549, 'irs': 6760, 'authorized': 2349, 'subject': 2350, 'judgments': 5694, 'thirty': 5695, 'bus': 6772, 'was': 2351, 'scum-sucking': 6209, 'gamble': 6201, 'mediterranean': 2352, 'concentrate': 2353, 'cheesecake': 5696, 'wantcha': 2354, "choosin'": 2356, 'ton': 5697, 'scully': 4644, 'fledgling': 2357, 'sauce': 5847, "'ceptin'": 6068, 'nagurski': 2358, 'lousy': 5698, 'lovelorn': 2359, 'friendly': 5700, "he'll": 2360, 'jeter': 2361, 'foodie': 2362, 'alarm': 5702, 'mistake': 5703, 'healthier': 5704, 'tow-talitarian': 6632, 'hats': 5705, 'fifty': 2706, 'nobody': 2363, '||new_line||': 5706, 'dice': 6633, 'peanut': 5707, 'sketching': 969, 'boxer:': 6479, 'horror': 2365, 'y-you': 2366, "i'd": 2367, 'realized': 5709, 'calling': 6673, 'daniel': 2371, 'dump': 2372, 'selma': 5711, 'moe': 2373, 'connection': 5713, 'savvy': 6635, 'sandwich': 4704, 'remorseful': 5714, 'beady': 5715, 'newsies': 5716, 'fancy': 5717, 'post-suicide': 2374, 'nantucket': 5458, 'no': 5718, 'criminal': 1256, 'sausage': 2709, 'caricature': 2375, 'dash': 6636, "'kay-zugg'": 5719, 'forty': 5720, 'contest': 6637, 'borrow': 2376, 'r': 973, 'warned': 5722, 'kinderhook': 5723, 'singers:': 5724, 'hop': 5726, 'workers': 5727, 'supreme': 5728, 'billboard': 2377, 'grammys': 5729, 'road': 2378, 'lecture': 5730, 'wage': 5731, 'colonel:': 2379, "can'tcha": 2380, 'three': 2381, 'my-y-y-y-y-y': 5734, 'wraps': 5735, 'directions': 2382, 'general': 2383, 'johnny': 5736, 'secret': 5737, 'macaulay': 5738, 'intervention': 5660, 'briefly': 2384, 'groans': 5739, 'smelling': 2385, 'understood': 5740, 'mellow': 975, 'yee-ha': 2386, 'goblins': 2387, 'yoink': 2388, 'lachrymose': 6320, "'ere": 2390, 'bothered': 2391, 'nuked': 6192, 'avenue': 5743, 'patrons:': 5744, 'century': 5745, 'moving': 2393, 'goodnight': 387, 'something:': 5747, 'hollowed-out': 2394, 'pint': 5748, 'kay': 388, 'reed': 5749, 'gets': 2395, 'row': 2396, 'unhappy': 6533, 'tavern': 4396, '_marvin_monroe:': 2397, 'jacksons': 6669, "i-i'll": 2398, 'orphan': 2399, 'brain-switching': 5753, 'gloop': 2400, 'nigel_bakerbutcher:': 5755, 'yep': 5756, 'dea-d-d-dead': 6078, 'belt': 5758, 'u2:': 5759, 'keep': 2401, 'letter': 5760, 'flayvin': 1984, 'stranger:': 5761, 'oils': 2715, 'benefits': 2402, 'five-fifteen': 2403, 'rocks': 6567, 'gin': 5762, 'cobbling': 5763, 'amnesia': 1517, 'toe': 2404, 'birthday': 5764, 'sec_agent_#2:': 5765, 'x-men': 2405, 'baloney': 2406, 'fruit': 2407, 'tastes': 2408, 'prison': 5767, 'peace': 2409, 'bald': 1562, 'husband': 4038, 'without': 5769, 'bouquet': 2410, 'stops': 5770, 'uncle': 2141, '_kissingher:': 2412, 'garbage': 2413, 'circus': 2414, 'choices': 5772, 'script': 2718, 'nachos': 2415, 'rife': 2416, 'swe-ee-ee-ee-eet': 978, 'squabbled': 6642, 'pretend': 2417, 'drag': 5774, 'regretted': 5775, "tree's": 2418, 'unattended': 2419, 'getup': 5776, 'wuss': 2421, 'vestigial': 2422, 'pockets': 5777, 't-shirt': 2424, 'jane': 5778, 'derisive': 5779, 'snaps': 2425, 'low': 5780, 'funds': 2426, 'beans': 5781, 'whaaa': 2427, 'anthony_kiedis:': 2428, 'shag': 5782, 'panicky': 2429, 'prepared': 5783, 'nickel': 979, 'girl': 5785, 'torn': 5786, 'goldarnit': 2722, 'bathed': 5787, 'isle': 2430, 'rude': 5788, 'wobbly': 2431, 'bought': 4967, 'fool': 2432, 'california': 5519, 'been': 2433, 'clown-like': 2434, 'plant': 5790, 'recommend': 5792, 'edna': 5793, 'drinking': 2435, "jackpot's": 2436, 'killed': 2437, '14': 2149, 'plants': 2438, 'wisconsin': 5796, 'browns': 2439, 'grammy': 2440, 'insults': 2441, 'sigh': 2443, 'fuzzlepitch': 5797, 'rub-a-dub': 5523, 'snake': 5798, 'married': 1536, 'sloe': 2153, 'bar_rag:': 2445, 'ceremony': 3885, 'friend:': 2446, 'counter': 5800, 'derek': 5801, 'nature': 2447, 'ringing': 5802, 'mixed': 5803, 'occasion': 2448, 'inflated': 5619, 'test': 2449, 'vulgar': 2450, 'hounds': 982, 'days': 5806, 'rims': 5807, 'newsletter': 5808, 'pumping': 5809, '6': 2451, 'worthless': 2452, 'ripped': 2453, 'presents': 6311, 'allow': 3815, 'sticker': 2454, 'student': 2456, 'jernt': 5811, 'professor': 5812, 'sneaky': 5813, '10:15': 3816, 'sells': 5814, 'turned': 5815, 'everything': 2457, 'perch': 3817, 'lady': 2460, 'statue': 5817, "neighbor's": 5819, 'charge': 2156, 'slick': 5821, 'beginning': 5822, 'bit': 2461, 'slice': 2462, 'airport': 6645, "smokin'": 5825, 'fbi_agent:': 3275, 'flames': 2463, 'unforgettable': 5827, 'bumped': 2464, 'africanized': 5823, 'vodka': 2465, 'humiliation': 6758, 'while': 5830, 'wins': 2466, 'zero': 2467, "games'd": 2468, 'blackjack': 399, 'pointed': 2469, 'dumptruck': 2472, 'elmer': 2473, 'tells': 2474, 'apology': 5837, 'bits': 2475, 'pine': 2476, 'triangle': 2477, 'wife-swapping': 2478, 'pizzicato': 5527, 'he': 2479, 'tomorrow': 3277, 'xanders': 2142, 'either': 2480, 'stained-glass': 2481, 'apply': 2482, 'ragtime': 4410, 'meyerhof': 2483, 'sticking': 5841, 'icy': 2484, 'catching': 2485, 'kent': 5843, 'scratcher': 5844, 'abercrombie': 2486, 'owned': 2487, 'out': 2488, 'choice:': 2489, 'helps': 5845, 'live': 3280, 'fair': 2490, 'malfeasance': 4978, 'mission': 2491, 'togetherness': 5849, 'ratted': 2492, '1-800-555-hugs': 5851, 'tall': 4977, 'dads': 6098, 'larson': 2493, 'penny': 3821, 'want': 2494, 'compete': 2495, 'slab': 2580, 'station': 4416, 'middle': 5124, 'hardhat': 5853, 'payday': 2496, 'repressed': 5855, 'senator': 2497, 'crowded': 1571, 'puffy': 5857, 'sending': 3822, 'susie-q': 3927, 'hibachi': 2498, 'chair': 5859, 'planned': 2499, 'season': 2500, 'labels': 5860, 'shove': 2501, 'warmly': 4021, 'brains': 2502, 'grub': 5528, "showin'": 5861, 'squeezed': 5862, 'hit': 2503, 'nerve': 2504, 'treat': 5569, 'paints': 2505, 'says': 5863, 'hole': 2506, 'shaken': 5864, 'founded': 3283, 'fiction': 2507, 'register': 5868, 'disdainful': 2508, 'scrutinizing': 4031, 'village': 5869, 'mall': 2509, 'graves': 5870, 'crapmore': 5871, 'affection': 5872, 'eyeing': 5529, 'hitchhike': 2510, '2nd_voice_on_transmitter:': 5875, "d'ya": 2512, 'queer': 3285, 'easy-going': 4842, 'kicked': 2514, 'sixty-nine': 6030, 'quero': 4055, 'hearse': 2516, 'patron_#2:': 2517, 'mimes': 5877, "breakin'": 2518, 'b-day': 2519, 'tale': 2520, 'ore': 3286, 'voice:': 2522, 'wish': 5878, 'stinger': 5879, 'pained': 2523, 'cat': 6107, 'gal': 2524, 'thesaurus': 5882, 'difference': 2526, 'einstein': 5883, 'scary': 2527, 'mostrar': 5884, 'paramedic:': 991, 'stalwart': 5885, 'bret': 5886, "couldn't": 4695, 'malibu': 2528, 'goods': 3828, 'w': 2529, 'effigy': 2530, 'thru': 5887, 'wonder': 2531, 'worse': 2532, 'cross-country': 5889, 'winch': 5890, 'kicks': 6658, 'won': 5891, 'hobo': 5892, 'diets': 2534, "patrick's": 2535, 'coney': 3830, 'cooler': 2536, 'totally': 6659, 'drawn': 5894, 'built': 2537, 'listened': 5895, 'watashi': 1577, 'aggie': 2538, 'shot': 5896, 'checks': 5897, 'ehhhhhhhh': 5899, 'blossoming': 5900, 'direction': 5901, 'box': 996, 'valley': 5903, 'piano': 6661, 'flush-town': 2540, 'pays': 5904, 'rabbits': 2541, 'inexorable': 5905, 'involving': 5906, 'begging': 6779, "moe's": 2542, 'outs': 2543, 'environment': 2544, 'mean': 2546, 'dum-dum': 5909, 'pretzel': 2548, 'investment': 3292, 'kneeling': 5910, 'actress': 5912, 'burt_reynolds:': 415, 'barn': 5913, 'sympathizer': 5914, 'hunter': 5916, 'backgammon': 2172, 'will': 5917, "livin'": 5918, 'roomy': 5920, 'lumpa': 5921, 'crab': 2550, 'declan': 4104, 'generously': 2551, 'new_health_inspector:': 2552, 'thirty-thousand': 2553, 'dealt': 5923, 'answer': 1763, 'brainiac': 2554, 'other': 2555, 'sets': 5925, 'absolut': 2556, 'open': 998, 'foundation': 5926, 'bullet-proof': 2557, 'provide': 2558, 'maman': 2559, 'outlook': 2560, 'peter': 2561, 'white': 2562, 'advance': 2256, 'c': 6629, 'showing': 2563, 'enlightened': 2564, 'eaters': 6664, 'suburban': 5930, 'themselves': 5931, 'small': 2741, 'fire_inspector:': 5932, 'democracy': 5933, 'mobile': 5934, 'forgotten': 2565, 'spoon': 5988, 'gallon': 2566, 'planning': 6666, 'oughta': 2567, 'talking': 5937, 'think': 1581, 'log': 5940, 'sudoku': 6156, 'lay': 2569, 'talked': 1582, 'personal': 5941, 'undated': 2570, 'wiener': 2571, 'bender:': 5944, 'unbelievable': 6667, 'jolly': 5945, 'well': 5946, 'utility': 2572, 'lou:': 2573, 'guess': 2574, 'elves:': 5947, 'stein-stengel-': 5948, 'stirring': 592, 'fell': 5949, 'renee:': 1016, 'snout': 5950, 'agent': 5541, 'beligerent': 2575, 'crime': 5951, 'conditioner': 5953, 'sub-monkeys': 5771, 'written': 5954, 'tree': 2576, 'hoagie': 6532, 'bam': 5955, 'crowds': 2577, 'celebrity': 2578, 'swell': 2579, 'lovely': 2136, 'hiding': 2581, 'rope': 2583, 'evil': 2584, 'dogs': 2585, 'bum:': 2586, 'inside': 2587, 'physical': 2588, "beggin'": 3838, 'absorbent': 2589, 'enthused': 2590, "you'd": 2591, 'reconsidering': 2592, 'official': 2593, '21': 4114, 'woo-hoo': 6159, 'reading:': 6702, 'wings': 2594, 'blade': 3839, 'lessee': 617, 'pigs': 2595, 'warn': 2596, 'routine': 6672, 'terrific': 426, 'fleabag': 1453, "town's": 5751, "'evening": 5958, 'dumb-asses': 2082, 'fustigation': 2598, 'usual': 5959, 'see': 5960, 'invited': 2599, 'occupancy': 427, 'duffed': 5961, 'yards': 2601, 'above': 2602, 'gary:': 6127, 'flexible': 2603, 'divine': 2604, 'ungrateful': 2605, "car's": 2606, 'heavyweight': 2607, 'tight': 5963, 'degradation': 2608, 'recipe': 5965, 'cranberry': 2609, 'unexplained': 2610, 'sincere': 2611, 'pity': 5967, 'thorn': 4902, "talkin'": 2612, 'spinning': 5970, 'sitcom': 5548, 'tablecloth': 5971, 'scene': 5972, 'bless': 2614, "idea's": 2615, 'addiction': 5974, 'come': 2616, 'complaint': 5975, 'better': 5976, 'ye': 5977, 'acquitted': 2617, 'jovial': 2618, 'meaningless': 5978, 'hafta': 5979, 'trucks': 2619, "raggin'": 5980, 'cobra': 2620, 'suit': 2621, 'kick-ass': 2622, 'stands': 5982, 'buy': 2623, 'drive': 2624, 'twice': 5983, 'chuck': 5984, 'backwards': 2625, 'towed': 2626, 'inspiring': 5985, 'clandestine': 5986, 'sleigh-horses': 2627, 'tough': 2628, 'point': 2629, 'blow': 1013, 'recently': 2630, 'resigned': 5989, 'common': 2631, 'lemonade': 4995, 'write': 2632, 'use': 5990, 'especially': 2633, 'open-casket': 6131, 'driving': 5991, 'voodoo': 6365, 'specializes': 2634, 'murmur': 5992, 'name:': 2635, 'portfolium': 2636, 'cola': 6681, 'chic': 1015, 'produce': 2637, 'dimly': 2638, 'usually': 5996, 'fuhgetaboutit': 2639, 'hired': 2640, 'rainforest': 5606, 'proud': 2641, "fine-lookin'": 2642, 'laugh': 2643, 'africa': 2644, 'fountain': 6751, 'punkin': 2645, 'supposed': 2646, "poundin'": 6000, 'ons': 6001, 'sadly': 6002, 'ollie': 2647, 'strongly': 6003, 'marvin': 1018, 'cuff': 6004, 'pay': 6006, 'product': 6007, 'proposition': 6009, 'word': 2649, 'closing': 6011, 'modern': 2650, 'rueful': 6134, 'adventure': 6012, 'brassiest': 2653, 'alright': 2652, 'somebody': 435, 'action': 2654, 're:': 2655, 'frosty': 1593, 'swelling': 6014, 'mither': 4443, 'rounds': 2656, 'natural': 6015, 'overhearing': 437, 'wildfever': 4445, 'muttering': 6017, 'plum': 2657, 'measurements': 6136, 'replace': 2658, 'sobbing': 2659, 'bumpy-like': 6018, 'carve': 6019, 'troubles': 2660, 'took': 2661, 'cocking': 4446, '_powers:': 6021, 'polygon': 6138, 'ned_flanders:': 2662, 'guns': 2663, 'hushed': 6022, 'winks': 1022, 'mocking': 6686, 'lessons': 6537, "d'": 2664, 'agent_miller:': 6026, 'obvious': 2665, 'dessert': 4885, 'miles': 2666, 'mansions': 6031, 'bigger': 6032, 'found': 2669, 'delts': 2670, 'energy': 2671, 'safecracker': 1595, 'grave': 2672, 'gay': 6034, 'play/': 6035, 'attend': 2673, 'tried': 2674, 'knuckle-dragging': 6036, "watchin'": 6037, 'fake': 6038, 'round': 6039, 'losing': 2675, 'cream': 2676, "tonight's": 2677, 'dames': 5554, 'karaoke': 6040, 'payments': 4441, 'spreads': 2678, 'amid': 6041, 'harmony': 6042, 'beat': 6043, 'unlocked': 4452, 'limber': 6046, 'emergency': 6047, 'asleep': 2679, "phone's": 2680, 'cough': 6049, 'thought_bubble_homer:': 2681, 'frogs': 6051, 'hitler': 1784, 'jazz': 627, 'declare': 2682, 'mexicans': 6052, 'grudgingly': 2683, 'flown': 2684, 'alcoholism': 2685, 'bloodball': 2686, 'shoe': 1599, 'jets': 2688, 'sound': 2689, 'fingers': 6053, 'ducked': 5560, 'noooooooooo': 5122, 'miracle': 2690, 'coat': 2691, 'conspiracy': 1600, 'tha': 6055, 'sings': 6056, 'invulnerable': 2692, 'publish': 6058, 'toilet': 2693, "hole'": 2694, 'holy': 4221, 'thrilled': 2695, 'indifferent': 2696, 'weather': 1603, 'dean': 6059, 'sips': 6060, 'watch': 2698, 'voicemail': 6061, 'speaking': 6062, "fishin'": 6063, 'imitating': 6064, 'eighty-three': 6065, 'lives': 2699, 'owns': 6066, 'hand': 2700, 'unfortunately': 2701, 'dumbbell': 6067, 'their': 6466, 'chauffeur': 1605, 'kwik-e-mart': 2702, 'barf': 6069, 'tommy': 2703, 'colorado': 2704, 'polls': 2705, 'trainers': 2707, 'director:': 6070, 'taught': 6071, 'smurfs': 2708, 'sweden': 6072, 'kenny': 2710, 'fireworks': 3316, 'about': 6073, 'health_inspector:': 6074, 'term': 4592, 'gheet': 2711, 'lie': 6075, 'pantsless': 6076, 'lookalike': 2712, 'coin': 2713, "writin'": 6077, 'gabriel:': 2714, 'loaded': 6079, "we've": 643, 'watching': 2716, 'turkey': 2717, 'clench': 6081, 'clown': 447, 'exultant': 1030, 'tatum': 2719, 'contemptuous': 6083, 'cosmetics': 3784, 'hootie': 6085, 'cats': 2721, '||quotation_mark||': 4459, 'poin-dexterous': 2723, 'remains': 2724, 'poisoning': 1031, 'bitter': 2725, 'annual': 6091, "wife's": 6092, 'tire': 6093, 'bluff': 2726, 'amber': 2727, 'crazy': 4462, 'chicks': 6096, 'macho': 6695, 'hideous': 5585, 'kisses': 1034, 'earrings': 2729, 'windex': 3320, 'work': 6097, 'hardwood': 2730, 'infestation': 5907, 'newsweek': 6099, 'nods': 6100, 'bidet': 6101, 'beating': 6102, 'pickled': 6103, 'ninety-seven': 6104, 'sucks': 6105, 'crayola': 6106, 'summer': 5789, 'done': 2731, 'mcbain': 2732, 'twins': 6108, 'chum': 6109, 'poet': 2733, 'fault': 6110, "city's": 6008, 'urge': 6111, 'dumpster': 1610, 'whale': 5568, 'pursue': 2734, 'meteor': 6679, 'julep': 2735, 'virility': 2736, 'papa': 2737, "shootin'": 6699, 'ideas': 6112, "what're": 2738, 'teacher': 2739, 'infiltrate': 6113, 'heatherton': 5008, "kids'": 1419, 'dawning': 6114, 'boxing': 2742, 'pretending': 2743, 'huddle': 6117, 'absolutely': 6118, 'game': 1037, 'sing': 6119, 'neanderthal': 6120, 'mason': 6121, "hasn't": 6122, 'mmm': 6123, 'buddy': 6124, 'wounds': 6126, 'louie:': 2744, 'greatest': 2745, 'week': 1169, 'henry': 6128, 'ghouls': 3864, 'online': 2746, 'silent': 6129, 'firmly': 4341, 'rip': 3866, 'blowfish': 2747, 'smelly': 6130, 'none': 2748, 'gumbel': 2749, 'thanksgiving': 6132, 'button': 6133, 'fonda': 2750, 'snow': 1614, 'italian': 6135, 'full': 2751, 'life-sized': 6137, 'pleasure': 6139, 'bowie': 1040, 'remain': 2752, 'agency': 2753, 'forecast': 3329, 'barflies': 6140, 'lifetime': 2754, 'co-sign': 6498, 'socialize': 2755, 'mumble': 6141, 'thorough': 2756, 'tv_husband:': 6142, 'courts': 6151, 'wide': 4244, 'rainbows': 2757, "fightin'": 1139, 'soup': 2758, 'bowling': 2759, 'mechanical': 6143, 'indigenous': 2760, 'cell-ee': 6154, 'pip': 6145, 'catch-phrase': 6146, 'sangre': 6147, 'finish': 2761, 'klown': 2762, 'care': 6155, 'homunculus': 2763, 'parenting': 6148, 'closet': 6778, 'suffering': 2764, 'ohmygod': 6149, "stayin'": 6150, 'catholic': 6158, '_eugene_blatz:': 2765, 'britannia': 2766, 'moe_recording:': 2767, "wearin'": 6152, 'dislike': 6153, 'matter': 2768, 'stored': 2769, 'beep': 2770, 'voters': 2771, 'turn': 6157, 'gordon': 2772, 'advertise': 6024, 'company': 1047, 'teenage_bart:': 1615, 'wayne:': 6160, 'flustered': 2773, '_julius_hibbert:': 2774, 'brave': 2775, 'please': 2776, 'pageant': 6162, 'named': 2777, 'possibly': 6163, 'ought': 6164, 'catch': 6165, 'racially-diverse': 2778, 'railroad': 5437, 'thought_bubble_lenny:': 1172, 'race': 2779, 'series': 6166, 'ease': 6167, 'last': 2780, 'golden': 4626, 'ã€': 6170, 'fellas': 2781, 'bon-bons': 2782, 'menacing': 2783, 'wake': 2784, 'cowardly': 6705, 'then:': 4077, 'chill': 5574, 'little_hibbert_girl:': 6172, 'pennies': 2786, 'dough': 2788, 'we': 2208, 'books': 6161, 'makes': 2789, 'nordiques': 2790, 'gulps': 6175, 'send': 2791, 'sight': 6176, 'mags': 2793, 'funny': 6707, 'canyoner-oooo': 6177, 'stickers': 6178, 'government': 6179, 'donation': 2794, 'kazoo': 6181, 'focus': 2795, 'professional': 2796, 'shriners': 2797, 'promised': 4578, 'kills': 6708, 'dressed': 2798, 'friendship': 2799, 'practice': 2800, 'advertising': 2801, 'decadent': 2802, 'hold': 3335, 'school': 6187, 'democrats': 4475, 'combines': 6189, 'mice': 2804, 'eternity': 2805, 'need': 2806, 'hotenhoffer': 6190, 'dracula': 6191, 'aboard': 466, 'remembered': 2807, 'mirthless': 6193, 'blessing': 6194, 'duff_announcer:': 2809, 'combination': 2810, 'spent': 1620, 'respect': 1262, 'exchanged': 2811, 'kidding': 2812, 'belch': 6195, "dog's": 6197, 'forty-seven': 2813, 'passion': 2814, 'terrified': 6198, 'forty-two': 5799, 'skeptical': 2815, "didn't": 2816, 'irrelevant': 1051, 'mortal': 5020, 'conspiratorial': 6202, 'victorious': 3875, 'hops': 2818, 'seats': 2819, 'coyly': 2820, 'clothespins': 2821, 'sugar-free': 6206, 'suspended': 2822, 'dry': 5578, 'flame': 2823, 'twelveball': 6210, 'onto': 2824, 'trust': 5430, 'country': 5582, 'error': 2825, 'refresh': 2826, 'drink': 5024, 'walked': 2827, 'steel': 6211, 'felt': 2828, 'expecting': 6212, 'bucket': 6169, 'showed': 5412, 'mcclure': 3861, 'ring': 6213, 'ten': 2830, 'congoleum': 2831, 'cover': 6215, 'h': 6090, 'disguise': 6351, 'pep': 6216, 'expose': 6217, 'booze-bags': 3878, 'chocolate': 1055, 'loneliness': 1625, 'declan_desmond:': 5414, 'himself': 5938, 'injury': 2832, 'because': 6219, 'shrugging': 2833, 'platinum': 1626, 'roses': 2217, 'pitcher': 6222, 'schedule': 2834, 'lenford': 2835, 'protesters': 6225, 'glitz': 5027, 'fantastic': 2219, 'incarcerated': 2836, 'ballclub': 2837, 'i': 6230, "mother's": 3747, '||dash||': 2838, 'atlanta': 2221, 'notch': 6232, 'jerks': 2885, 'cooking': 3048, 'humanity': 2839, 'ireland': 2840, 'repeated': 2841, 'pure': 1412, 'spits': 2842, 'bleacher': 2785, 'diablo': 2843, 'monroe': 6236, 'plenty': 6237, '_timothy_lovejoy:': 3456, 'tang': 6238, 'ahead': 6239, 'elaborate': 2844, 'rafters': 5587, 'grandã©': 6080, 'harvesting': 6240, 'sometime': 2845, 'mustard': 6241, 'pirate': 2847, 'american': 1432, 'pernt': 2848, 'funniest': 6242, 'searching': 1057, 'premise': 2849, 'being': 6244, 'right': 2850, 'vicious': 2851, 'locklear': 6246, 'brainheaded': 2853, 'cracked': 2854, 'separator': 5590, "cuckold's": 6247, 'tense': 6248, 'warning': 6249, 'african': 2855, 'musketeers': 2856, 'private': 6251, 'ingested': 2857, 'dashes': 2858, "stinkin'": 2859, 'dazed': 5593, 'deeply': 2860, 'infatuation': 2861, 'starla': 2862, 'saga': 2863, 'doreen:': 6243, 'band': 6713, 'allowance': 6259, 'strains': 2787, 'employees': 929, 'act': 2865, 'ones': 2866, 'hate': 6261, 'self': 2867, "disrobin'": 4486, 'understood:': 2868, 'unfamiliar': 2869, 'mt': 6262, 'fact': 6322, 'ginger': 6264, 'prime': 2870, 'its': 2871, 'happy': 6265, 'hammer': 2872, 'drives': 3883, 'stagy': 6266, 'knocked': 2875, 'eurotrash': 5402, 'prove': 2876, 'acquaintance': 6267, 'remote': 6268, 'favorite': 6507, 'crawl': 2877, 'reader': 2878, 'dollar': 2879, 'kodos:': 5035, 'boxers': 2880, 'helping': 6271, 'waterfront': 2882, 'busted': 6272, 'pee': 6273, 'therapist': 2883, 'clammy': 2884, 'korea': 2886, 'faces': 6275, 'compare': 5597, 'two-thirds-empty': 6276, 'triple-sec': 3890, 'your': 2887, 'billy_the_kid:': 6278, 'scores': 6279, 'recall': 2888, 'sassy': 6281, 'original': 2889, 'noises': 2890, 'pissed': 6282, 'head-gunk': 5599, 'crippling': 2891, "s'pose": 4490, 'shotgun': 2234, 'beaumarchais': 2893, 'je': 2894, 'necessary': 6284, 'marriage': 1529, 'able': 6285, 'throat': 2895, "bettin'": 6287, 'jeff': 2896, 'prohibit': 6288, 'bleeding': 5601, "there's": 6289, 'bridges': 2897, 'cleaned': 6290, 'fox': 6292, 'aunt': 2898, 'don': 2899, 'chanting': 2792, 'gasp': 5997, 'nuts': 2900, "ma'am": 2901, 'cheer': 2902, "i'unno": 2903, 'sat-is-fac-tion': 2904, 'screams': 6295, 'dame': 3893, 'project': 1068, 'heave-ho': 2906, 'package': 2907, 'choking': 5605, 'grumbling': 2908, 'squadron': 3729, 'octa-': 6718, 'starting': 6180, 'helpful': 6298, 'fritz': 5608, "you'll": 2909, 'fresco': 3896, 'grace': 2910, 'sensitivity': 6299, 'ze-ro': 6300, 'shhh': 6301, 'march': 6302, 'forgot': 484, 'consoling': 6182, 'tapered': 6719, 'phrase': 2911, 'sinister': 2912, 'yellow': 2913, 'someone': 2914, 'park': 2915, 'madison': 5043, 'sounds': 485, 'schnapps': 4361, 'dropped': 2916, 'disappointed': 6304, 'neon': 6305, 'totalitarians': 6306, 'prices': 2917, 'raggie': 6308, 'coms': 6309, 'cadillac': 2918, 'mahatma': 2919, "starla's": 2920, 'states': 2921, 'lighter': 4278, 'knowingly': 6312, 'comforting': 2922, 'girlfriend': 2923, 'yellow-belly': 6314, 'them': 2924, 'straining': 2925, 'innocent': 4494, 'a': 6315, 'to': 2926, 'she-pu': 85, 'texan': 2927, 'sweater': 6317, 'idealistic': 2928, 'sheets': 6318, 'timbuk-tee': 2929, 'play': 3487, 'gimme': 5641, 'snatch': 6323, 'thing:': 6324, 'heart': 6183, 'naked': 6325, 'grrrreetings': 2930, 'stumble': 2931, 'pop': 6184, 'problemo': 6326, "fans'll": 3757, 'pretends': 6327, 'accepting': 6185, 'binoculars': 2933, "buyin'": 6328, 'clean': 6723, 'saucy': 2934, 'ooo': 2935, 'ribbon': 6330, 'ken:': 2937, 'gone': 2938, 'switch': 2939, 'died': 6331, 'de-scramble': 2940, 'stevie': 6188, 'lucius': 5047, 'gag': 6333, 'specialists': 2244, 'affects': 6755, 'five': 6334, 'sugar-me-do': 6336, 'weirder': 2803, 'corkscrew': 2942, 'tenuous': 2943, 'conversations': 6338, 'moe-heads': 2945, 'aziz': 2946, 'studio': 2948, "ragin'": 2949, 'jamaican': 2950, 'burnside': 1506, 'email': 4478, 'traffic': 6339, 'chest': 6340, 'indicates': 2951, 'powerful': 6589, 'image': 2952, 'minute': 2953, "won't": 6341, 'pool': 2954, 'chase': 2955, 'happened': 2956, 'statues': 5598, 'darts': 6342, 'said:': 6343, 'cow': 6344, "i-i'm": 6345, 'bedroom': 2957, 'hellhole': 2958, 'fuss': 2959, 'hated': 2960, 'atari': 2961, 'pen': 2962, 'music': 2963, 'wha': 6350, 'stool': 2964, 'police': 2965, 'exclusive:': 2966, 'collette:': 2247, 'bowl': 6353, 'nasa': 6354, 'corporate': 2968, 'street': 2969, 'menace': 2970, 'normals': 2971, 'hexa-': 6356, 'appendectomy': 6357, 'enveloped': 2973, 'freed': 6358, 'tropical': 2974, 'lighten': 2975, 'father': 6360, 'simple': 2977, 'soul-crushing': 2978, 'release': 6361, 'consider': 1131, 'scornful': 2979, 'hugh': 1077, 'exact': 6363, 'har': 6364, 'designated': 6366, 'pfft': 6367, 'moans': 2808, 'calculate': 5054, 'miss': 2980, 'awfully': 6370, 'correcting': 6371, "treatin'": 518, "kearney's_dad:": 6372, "waitin'": 2981, 'cleveland': 6375, 'woman:': 6376, "that's": 6377, 'french': 2983, 'celebration': 1383, 'rockers': 2984, 'worried': 5196, 'jubilation': 2985, 'dancing': 5059, 'gossipy': 2986, "y'know": 2988, 'shoo': 2990, 'name': 6378, 'story': 2991, 'price': 6379, "professor's": 2992, 'hard': 2993, 'ballot': 6381, 'beneath': 2994, 'shakespeare': 2668, 'six': 5061, 'fork': 6382, 'some': 6542, 'uncomfortable': 2995, 'job': 2996, 'mock': 2259, 'belong': 4368, 'fold': 4356, 'noosey': 2997, 'shaky': 6387, 'sumatran': 2998, 'living': 2999, 'welcome': 3000, 'united': 3001, 'way:': 3003, 'sneering': 6196, 'piling': 3004, 'tuna': 6392, 'called': 3005, 'hears': 6393, 'southern': 3006, 'sir': 6395, 'poured': 3009, 'wipes': 3008, 'marjorie': 5230, 'slugger': 6396, 'burning': 6397, 'all-all-all': 3010, 'storms': 6398, 'instead': 3011, 'choke': 3012, 'shower': 3013, 'gorgeous': 3014, 'con': 3015, 'throw': 3016, 'patented': 6677, 'blind': 6399, "wonderin'": 6400, 'restaurant': 3017, 'lingus': 6401, 'incapable': 3018, 'precious': 3019, 'contact': 6402, 'sissy': 6403, 'minutes': 3020, 'distraught': 3022, 'sweetheart': 6404, 'taking': 3023, 'williams': 3025, 'carnival': 3026, 'fantasy': 3027, 'corner': 3028, 'grumpy': 6405, 'sanitary': 6406, 'during': 3029, 'secrets': 2817, 'purveyor': 3030, 'fall': 1650, 'sideshow_bob:': 620, 'handle': 3031, 'arabs': 3032, 'aged_moe:': 6408, 'charlie': 6409, 'list': 3033, 'insured': 6410, 'debonair': 3035, 'monster': 6411, 'bashir': 6412, 'bolting': 6413, 'exits': 3036, 'car': 2246, 'after': 6414, 'hygienically': 6415, 'standing': 6737, 'slaps': 3037, 'rubs': 6416, 'ohhhh': 6417, 'sixteen': 3038, 'mitts': 1470, 'jacks': 5623, 'coupon': 6418, 'hm': 6419, 'genuinely': 6739, 'spellbinding': 5624, "cashin'": 6421, 'fridge': 3039, 'leaving': 3040, 'mouths': 1629, 'guts': 6424, 'a-lug': 3042, 'calls': 5654, 'sack': 3043, "they'd": 3044, 'gestated': 3045, 'second': 6426, 'greedy': 3046, 'uniforms': 548, 'troy:': 6427, 'hans:': 4605, 'appreciate': 525, 'onassis': 3047, 'all-star': 3049, 'reward': 6428, 'movie': 4504, 'du': 6430, 'handing': 3050, 'vance': 3051, 'memory': 1352, 'belly': 6431, 'springfield': 3052, 'safer': 6432, 'saving': 502, 'boisterous': 6433, 'handoff': 3053, 'safety': 3054, 'wenceslas': 6458, 'hurt': 3055, 'resolution': 3057, 'princesses': 6741, 'dignity': 3058, 'customers': 6208, 'grinch': 3059, 'lance': 3060, 'not': 6435, 'gotcha': 6436, 'legs:': 3063, 'came': 3062, 'placed': 6437, 'well-wisher': 6438, 'repairman': 3064, 'couple': 6439, 'man_with_crazy_beard:': 3065, 'jobs': 3066, 'creepy': 3067, 'flash': 160, 'youuu': 3068, 'complete': 6440, 'sarcastic': 3069, 'simon': 3070, 'procedure': 3071, 'suspect': 3072, "g'night": 2275, 'missing': 6441, 'gargoyles': 3073, 'arrived': 6442, 'demand': 3074, 'pilsner-pusher': 3076, 'optimistic': 3077, 'lost': 3078, 'place': 5630, 'lager': 6540, 'banks': 1093, 'candles': 3079, 'chin': 3080, 'asses': 3082, 'palm': 507, 'asking': 6444, 'calmly': 1656, 'life-extension': 3083, 'completing': 3084, 'rewound': 6445, 'encouraged': 3085, 'loves': 6203, 'fist': 3086, "costume's": 4128, 'annoying': 6448, 'rented': 6449, 'transfer': 3087, 'peaked': 6451, 'poetics': 3088, 'serum': 3089, 'wearing': 2892, 'telegraph': 6453, 'cool': 6454, 'nightmare': 3090, 'nibble': 1095, 'eight': 6455, 'listening': 5634, 'relax': 6173, 'seems': 6456, 'gimmick': 6457, 'motel': 3092, 'pick': 3093, 'unsafe': 3094, 'sanctuary': 3095, "drawin'": 6460, 'gut': 3096, 'orders': 6461, 'moe-near-now': 6462, 'prolonged': 6463, "washin'": 6464, '/mr': 6465, 'satisfaction': 3097, 'asked': 3098, 'so': 3099, 'worldly': 3101, 'kid': 6407, 'body': 1097, 'mickey': 3102, 'warily': 1661, 'crayon': 4510, 'calendars': 6469, 'bottom': 6470, 'avalanche': 3104, 'ronstadt': 6471, 'badly': 6467, "other's": 513, 'support': 6474, 'bugs': 3105, 'sat': 3106, 'soothing': 6476, 'strap': 6477, 'everyone': 5804, 'knowledge': 2284, 'cloudy': 6478, 'flatly': 6480, 'bet': 3107, 'wrestle': 6481, 'wanted': 1101, "he'd": 6482, 'versus': 3108, 'got': 6483, 'ralph_wiggum:': 3109, 'stacey': 3110, 'ling': 3111, 'al': 6484, 'dreary': 6485, 'billingsley': 3112, 'tired': 3113, 'two-drink': 3114, 'getting': 1664, 'weirded-out': 3116, 'julienne': 6214, 'passenger': 3117, 'donut': 6488, 'whirlybird': 6489, 'underpants': 6490, 'pridesters:': 3118, 'laramie': 5639, 'boxcar': 1106, 'beanbag': 6491, 'formico:': 1667, 'landlord': 3119, 'novel': 3120, 'inspired': 3121, 'louse': 6492, 'eleven': 1668, 'two': 3122, 'equal': 3123, 'splendid': 6494, 'orgasmville': 6495, 'fellow': 3124, 'astronauts': 6496, 'wholeheartedly': 6497, 'dangerous': 3125, 'wally': 3126, 'hurts': 3127, 'mostly': 3128, 'legs': 3129, 'going': 4154, 'fish': 6499, 'muhammad': 3130, 'distaste': 6501, 'michael': 3131, 'access': 6502, 'glen:': 2291, 'ow': 3132, 'beautiful': 3135, 'stiffening': 3134, 'always': 515, 'cozy': 6506, 'steampunk': 3139, 'make:': 5676, 'good': 3926, 'jack': 6508, 'alls': 3141, 'awake': 3142, 'wrapped': 3143, 'figure': 3144, 'pushes': 3145, 'sunk': 3146, 'cummerbund': 6512, 'horrible': 2258, 'crystal': 3147, 'stolen': 1671, 'flynt': 3148, 'contemporary': 814, 'handed': 4052, 'tap': 3149, 'over': 6575, 'finishing': 3150, 'daddy': 3151, "you're": 3152, 'gentleman:': 6513, 'empty': 6514, 'glass': 6515, 'fondest': 6516, 'deeper': 6517, "plank's": 1673, 'instrument': 3153, 'reopen': 3891, 'spine': 3154, 'informant': 6520, 'without:': 3156, 'fl': 6521, 'naturally': 5834, 'murdered': 4520, 'am': 3157, 'sagacity': 6523, 'keeps': 3932, 'sure': 6525, 'snotball': 6218, 'trick': 6526, 'brockman': 6527, 'sternly': 1920, 'english': 6529, 'whiny': 5644, 'record': 6530, "family's": 3158, 'aerosmith': 3387, 'takeaway': 3159, "mopin'": 3160, 'wealthy': 3161, 'rainier_wolfcastle:': 3162, 'transmission': 6220, 'nervous': 3163, 'fierce': 3164, 'upset': 6535, 'starts': 6536, 'knives': 6538, 'life': 6539, "duelin'": 1117, 'part': 3165, 'settles': 6221, 'wonderful': 6543, 'market': 5647, 'one-hour': 3166, 'match': 5683, 'convenient': 3167, 'disappear': 6545, 'led': 6546, 'beard': 6547, 'rummy': 3168, 'peter_buck:': 3170, 'pusillanimous': 3171, 'takes': 6549, 'defected': 3173, 'recruiter': 4065, 'compels': 3174, 'pepsi': 2300, 'fringe': 6550, 'verticality': 3175, 'magnanimous': 1677, 'hungry': 3176, 'nemo': 6551, 'cuddling': 1121, 'deadly': 6553, 'wife': 6223, 'junebug': 6095, "daughter's": 3177, 'prettiest': 6753, 'copy': 6227, 'pitch': 6555, 'bread': 6226, 'sent': 6556, 'stealings': 5649, 'driveability': 6228, 'wudgy': 530, 'south': 3194, 'rapidly': 6229, '||right_paren||': 3178, 'dexterous': 3179, 'e': 3180, 'andy': 6559, 'feels': 3181, 'thinking': 3182, 'meditative': 3183, 'mulder': 6563, 'determined': 6564, 'forgets': 3185, 'super-genius': 6566, 'sloppy': 6568, "lookin'": 6570, 'nooo': 6571, 'fireball': 3186, "round's": 5650, 'maude': 3188, 'cop': 6573, 'sincerely': 6574, 'appropriate': 3941, 'dog': 4525, 'lushmore': 3190, 'stand': 3191, "bar's": 5299, 'beef': 3193, 'uh-oh': 6580, 'stares': 6233, 'paste': 3944, 'ecru': 3195, 'fad': 3196, 'assassination': 3197, 'football_announcer:': 4527, 'rosey': 4076, 'glyco-load': 3198, 'stationery': 1749, 'cookies': 3199, 'considering': 3200, 'massachusetts': 6234, 'hell': 3201, 'snorts': 3204, 'leprechaun': 3203, 'twin': 3206, 'aah': 3207, 'committing': 3208, 'slip': 3209, 'pathetic': 4455, 'kill': 6588, 'heh': 3210, 'german': 534, 'flailing': 6591, 'michelin': 3211, 'admiring': 5089, 'la': 6592, "bashir's": 3585, 'scratching': 6594, 'mac-who': 3212, 'bye': 6595, 'charity': 3214, 'intruding': 3215, 'breaks': 3216, "cheerin'": 6597, 'sucking': 3217, 'surgery': 939, 'rotch': 6599, 'milhouse_van_houten:': 6600, 'badmouths': 6724, 'clothes': 3218, 'woo': 2314, 'inserts': 3219, 'defeated': 6235, 'over-pronouncing': 3222, 'dispenser': 3221, 'scornfully': 2313, 'krabappel': 6601, 'uninhibited': 3223, 'overstressed': 3224, 'located': 6603, 'bubbles-in-my-nose-y': 5725, "son's": 6604, 'tipsy': 3225, 'skirt': 3950, 'hairs': 6606, 'wire': 3226, 'un-sults': 3228, 'horribilis': 3229, 'cheese': 5308, 'murderously': 3230, 'buried': 3231, 'denver': 6609, 'scotch': 3232, 'gabriel': 3233, 'average': 3234, 'string': 3235, '-ry': 3236, 'pad': 6611, 'comedies': 6613, 'until': 3237, '35': 3238, '250': 3239, 'signed': 3240, 'len-ny': 3241, 'yeah': 4577, 'estranged': 6586, 'violations': 6616, 'prompting': 3243, 'giggles': 3244, 'rancid': 6757, 'reaction': 3245, 'predictable': 6617, 'counting': 3246, 'customer': 2319, 'drinking:': 3247, '3rd_voice:': 6618, 'election': 540, 'droning': 3249, 'monorails': 2989, 'grin': 3250, 'only': 6620, 'freeze': 3403, 'nuclear': 5657, 'mater': 6623, 'lotsa': 6624, 'products': 6625, 'nã£o': 3251, 'lips': 4534, "yieldin'": 6626, 'stage': 3252, 'wave': 3253, 'enterprising': 6628, 'brewed': 6619, 'lobster-based': 3255, 'ignoring': 6630, 'blows': 6631, "tap-pullin'": 3256, 'distinct': 1828, 'hotline': 3257, 'dinks': 6171, 'ya': 6634, 'brief': 3258, 'stonewall': 3259, 'forbids': 3260, 'casting': 3733, 'sunglasses': 6627, 'blurbs': 6638, 'effect': 3261, "cupid's": 6639, 'vegas': 3262, 'los': 6640, 'pipes': 845, 'back': 3263, 'hunka': 3264, 'worry': 3265, 'boned': 6641, 'often': 3266, 'dennis': 4100, 'lifts': 3267, 'rusty': 1689, 'loafers': 4434, 'tentative': 3268, 'annoyed': 6337, 'cappuccino': 3269, 'strawberry': 3270, 'branding': 6643, 'scared': 3955, 'lungs': 3271, 'coffee': 3272, 'but': 3273, 'assent': 3274, 'breathless': 6644, "time's": 547, 'presentable': 1134, 'pews': 6646, "they've": 3276, 'double': 6647, 'nine': 3278, 'bedtime': 6648, 'beaumont': 3279, 'man_at_bar:': 3281, 'tsk': 6649, 'maitre': 3282, 'brotherhood': 6650, 'met': 6651, 'sobo': 6652, "'": 3284, 'sexy': 3409, 'lib': 3782, 'crestfallen': 6654, 'young_moe:': 675, 'entrance': 6655, 'foot': 3287, 'ground': 3288, 'dating': 3289, 'buffet': 6656, 'cocks': 3290, 'years': 6657, 'cowboys': 1924, 'repay': 3937, 'chug': 3961, 'nearly': 6660, 'countryman': 3291, 'stones': 3293, 'lenny': 6511, 'dance': 6662, 'van': 2697, 'singer': 3294, 'male_inspector:': 6663, 'apartment': 3295, 'annie': 3296, 'fund': 3297, 'fritz:': 3298, 'windshield': 3924, 'dishonor': 6668, 'sunny': 5561, 'rat-like': 6670, 'awesome': 1695, 'oopsie': 6425, 'pulls': 6576, 'sing-song': 1696, 'espousing': 3299, 'mudflap': 6674, 'goo': 3300, 'surprised': 6675, "homer's": 6676, 'chuckles': 3301, "barney's": 552, 'chicken': 2074, 'tongue': 3302, 'mmmmm': 2326, 'bank': 3303, 'haplessly': 3304, 'upbeat': 6680, 'tomatoes': 2947, 'reality': 3305, 'breakdown': 4973, 'occasional': 3306, 'hugh:': 6682, 'rice': 3307, 'lists': 3308, 'eightball': 6683, 'courage': 6684, 'attitude': 1702, 'ignorant': 6685, 'depressed': 3966, 'patron_#1:': 1701, 'fights': 3309, 'intakes': 3310, 'darjeeling': 6687, "she's": 6688, 'friend': 6689, 'portuguese': 3311, 'danny': 3312, 'jukebox': 6349, 'grieving': 3313, 'adult_bart:': 3314, 'happen': 6307, 'poster': 3315, 'marge': 6690, 'tremendous': 6691, 'asks': 6764, 'perverted': 6692, 'boggs': 6693, "carl's": 1144, 'politicians': 557, 'worst': 6694, 'bathtub': 3317, 'clap': 3318, 'soaked': 6770, "bart'd": 3319, 'around': 5667, "c'mom": 3321, 'tv-station_announcer:': 3322, 'skinheads': 3832, 'joined': 6696, 'trip': 6697, 'stan': 4543, 'rome': 3323, 'hike': 6698, "startin'": 2423, 'tonic': 3324, 'winces': 1146, 'entertainer': 3325, 'ingrates': 3326, 'self-made': 6700, 'spooky': 3327, 'venom': 5669, 'birth': 3328, 'flying': 6701, 'cutest': 4698, 'with': 3330, 'fighter': 6703, 'heliotrope': 6704, 'theatrical': 6250, "askin'": 3331, 'eaten': 6706, 'bartending': 3332, 'dearest': 3333, 'patty': 3334, 'made': 3336, 'although': 3337, 'lying': 2333, 'reserve': 3338, 'andalay': 6252, 'sleeps': 5101, 'amanda': 560, 'shooting': 3339, 'religion': 6253, 'designer': 3340, 'patient': 1148, 'lloyd': 3341, 'background': 3202, "chewin'": 3342, 'suspicious': 3343, 'odor': 6710, 'ahem': 566, 'eyed': 3344, 'snap': 3345, 'fletcherism': 6711, 'bold': 5390, 'seriously': 6712, 'ticks': 5675, 'nervously': 3346, 'slop': 6714, 'saved': 3347, 'moe_szyslak:': 4548, 'jockey': 3348, 'ivanna': 6347, 'playhouse': 448, 'cure': 3349, 'pinball': 3350, 'barney': 6716, 'anti-crime': 1177, 'harv:': 3351, 'used': 6717, 'sotto': 3352, 'tooth': 3353, 'listens': 6255, 'lisa_simpson:': 3354, 'painless': 3355, 'louder': 3356, 'license': 3357, 'erasers': 6720, 'wheeeee': 1150, 'specials': 6722, 'furious': 1705, 'scatter': 3358, 'sexton': 6725, 'hundred': 3359, 'um': 3360, 'beers': 4983, 'average-looking': 2266, 'rules': 6726, 'venture': 5840, 'broadway': 3362, "mecca's": 6727, 'supermarket': 4553, 'salad': 6728, 'moon': 6729, 'great': 6730, 'lush': 6731, 'increasingly': 3363, 'beeps': 6732, 'face': 3364, 'bar:': 3365, 'sobs': 6257, "thinkin'": 6733, 'applesauce': 6734, 'soot': 6735, 'chosen': 6384, 'oh-ho': 6736, "show's": 3366, 'fortune': 573, 'anger': 4922, 'feelings': 4483, 'profiling': 4838, 'eighteen': 3367, 'money': 6738, 'sees': 3368, 'consciousness': 1156, 'rag': 6740, 'cent': 3471, 'zack': 3369, 'bite': 3370, 'sheet': 6743, 'partners': 3371, 'melodramatic': 6260, 'cases': 3372, '7g': 6745, 'if': 3374, 'sacrifice': 1708, 'nurse': 3375, 'majesty': 3376, 'bartender': 3377, 'lose': 6746, 'yells': 3378, 'jogging': 3379, 'gruesome': 6747, 'disguised': 3380, 'chauffeur:': 3978, 'decent': 6522, 'parrot': 3381, 'hilton': 3982, 'innocence': 3383, 'frankie': 3384, 'sec': 3385, 'fastest': 3386, 'terrifying': 6270, 'grampa': 4560, 'file': 6748, 'dear': 3388, 'flew': 6750, 'stuff': 3389, 'solved': 5685, 'in-in-in': 6263, 'eww': 3390, 'bow': 3391, 'moonshine': 94, 'ball': 3392, "yesterday's": 3393, 'dee-fense': 6752, 'syrup': 1160, 'canyonero': 3394, 'hero-phobia': 3395, 'wondered': 5686, 'dirty': 3427, 'chub': 6754, 'kick': 5687, 'closed': 3396, 'hillary': 3397, 'whaaaa': 6756, 'promise': 3398, 'kidney': 4774, 'spread': 3399, 'soir': 3400, 'store': 3401, 'month': 3985, "tomorrow's": 6759, 'easy': 3402, 'jig': 3404, 'tokens': 3730, 'dallas': 3860, 'certified': 3405, 'skin': 1714, 'busiest': 3406, 'fence': 3407, 'county': 6082, 'predecessor': 3408, 'janette': 3410, 'tuborg': 3411, 'awareness': 6763, 'done:': 583, 'ventriloquism': 2874, 'installed': 6765, "thing's": 3412, 'crappy': 3413, "man's_voice:": 2740, "wasn't": 3414, 'afraid': 1715, 'complicated': 6766, 'doom': 6767, 'wa': 6768, 'sidekick': 6369, 'rupert_murdoch:': 3415, 'festival': 6769, "how'd": 3416, 'weary': 4566, 'lifters': 6777, 'brace': 5692, 'mill': 3432, 'hook': 3417, 'mariah': 3418, 'cap': 3419, 'thoughtless': 3420, 'emotional': 3421, 'belly-aching': 3422, 'youse': 5693, 'seas': 4567, 'potatoes': 3423, 'walks': 3424, 'office': 4569, '&': 3425, 'oooh': 3426, 'shores': 6771, 'fumigated': 3428, "brady's": 6773, 'excited': 3429, 'new': 3430, 'vampires': 6775, 'carlotta:': 6776, 'undies': 3431, 'corporation': 3433, '||semicolon||': 3434, 'full-time': 5631}
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

# Check Point[¶](#Check-Point)

This is your first checkpoint. If you ever decide to come back to this notebook or have to restart the notebook, you can start from here. The preprocessed data has been saved to disk.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [6]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">import</span> <span class="nn">helper</span>
<span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>
<span class="kn">import</span> <span class="nn">problem_unittests</span> <span class="k">as</span> <span class="nn">tests</span>

<span class="n">int_text</span><span class="p">,</span> <span class="n">vocab_to_int</span><span class="p">,</span> <span class="n">int_to_vocab</span><span class="p">,</span> <span class="n">token_dict</span> <span class="o">=</span> <span class="n">helper</span><span class="o">.</span><span class="n">load_preprocess</span><span class="p">()</span>
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Build the Neural Network[¶](#Build-the-Neural-Network)

You'll build the components necessary to build a RNN by implementing the following functions below:

*   get_inputs
*   get_init_cell
*   get_embed
*   build_rnn
*   build_nn
*   get_batches

### Check the Version of TensorFlow and Access to GPU[¶](#Check-the-Version-of-TensorFlow-and-Access-to-GPU)

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [40]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">from</span> <span class="nn">distutils.version</span> <span class="k">import</span> <span class="n">LooseVersion</span>
<span class="kn">import</span> <span class="nn">warnings</span>
<span class="kn">import</span> <span class="nn">tensorflow</span> <span class="k">as</span> <span class="nn">tf</span>

<span class="c1"># Check TensorFlow Version</span>
<span class="k">assert</span> <span class="n">LooseVersion</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">__version__</span><span class="p">)</span> <span class="o">>=</span> <span class="n">LooseVersion</span><span class="p">(</span><span class="s1">'1.0'</span><span class="p">),</span> <span class="s1">'Please use TensorFlow version 1.0 or newer'</span>
<span class="nb">print</span><span class="p">(</span><span class="s1">'TensorFlow Version:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">__version__</span><span class="p">))</span>

<span class="c1"># Check for a GPU</span>
<span class="k">if</span> <span class="ow">not</span> <span class="n">tf</span><span class="o">.</span><span class="n">test</span><span class="o">.</span><span class="n">gpu_device_name</span><span class="p">():</span>
    <span class="n">warnings</span><span class="o">.</span><span class="n">warn</span><span class="p">(</span><span class="s1">'No GPU found. Please use a GPU to train your neural network.'</span><span class="p">)</span>
<span class="k">else</span><span class="p">:</span>
    <span class="nb">print</span><span class="p">(</span><span class="s1">'Default GPU Device:</span> <span class="si">{}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">test</span><span class="o">.</span><span class="n">gpu_device_name</span><span class="p">()))</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>TensorFlow Version: 1.0.0
</pre>

</div>

</div>

<div class="output_area">

<div class="output_subarea output_stream output_stderr output_text">

<pre>C:\Users\MKenneth\Miniconda3\envs\tensorfloww\lib\site-packages\ipykernel_launcher.py:14: UserWarning: No GPU found. Please use a GPU to train your neural network.

</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Input[¶](#Input)

Implement the `get_inputs()` function to create TF Placeholders for the Neural Network. It should create the following placeholders:

*   Input text placeholder named "input" using the [TF Placeholder](https://www.tensorflow.org/api_docs/python/tf/placeholder) `name` parameter.
*   Targets placeholder
*   Learning Rate placeholder

Return the placeholders in the following tuple `(Input, Targets, LearningRate)`

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [41]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">get_inputs</span><span class="p">():</span>
    <span class="sd">"""</span>
 <span class="sd">Create TF Placeholders for input, targets, and learning rate.</span>
 <span class="sd">:return: Tuple (input, targets, learning rate)</span>
 <span class="sd">"""</span>
    <span class="n">inputs</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">placeholder</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">int32</span><span class="p">,</span><span class="n">shape</span><span class="o">=</span><span class="p">[</span><span class="kc">None</span><span class="p">,</span><span class="kc">None</span><span class="p">],</span> <span class="n">name</span><span class="o">=</span><span class="s1">'input'</span><span class="p">)</span>
    <span class="n">targets</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">placeholder</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">int32</span><span class="p">,</span><span class="n">shape</span><span class="o">=</span><span class="p">[</span><span class="kc">None</span><span class="p">,</span><span class="kc">None</span><span class="p">],</span> <span class="n">name</span><span class="o">=</span><span class="s1">'targets'</span><span class="p">)</span>
    <span class="n">learningrate</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">placeholder</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">float32</span><span class="p">,</span> <span class="n">name</span><span class="o">=</span><span class="s1">'learningrate'</span><span class="p">)</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">inputs</span><span class="p">,</span> <span class="n">targets</span><span class="p">,</span> <span class="n">learningrate</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_get_inputs</span><span class="p">(</span><span class="n">get_inputs</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Build RNN Cell and Initialize[¶](#Build-RNN-Cell-and-Initialize)

Stack one or more [`BasicLSTMCells`](https://www.tensorflow.org/api_docs/python/tf/contrib/rnn/BasicLSTMCell) in a [`MultiRNNCell`](https://www.tensorflow.org/api_docs/python/tf/contrib/rnn/MultiRNNCell).

*   The Rnn size should be set using `rnn_size`
*   Initalize Cell State using the MultiRNNCell's [`zero_state()`](https://www.tensorflow.org/api_docs/python/tf/contrib/rnn/MultiRNNCell#zero_state) function
    *   Apply the name "initial_state" to the initial state using [`tf.identity()`](https://www.tensorflow.org/api_docs/python/tf/identity)

Return the cell and initial state in the following tuple `(Cell, InitialState)`

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [42]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">get_init_cell</span><span class="p">(</span><span class="n">batch_size</span><span class="p">,</span> <span class="n">rnn_size</span><span class="p">):</span>
    <span class="sd">"""</span>
 <span class="sd">Create an RNN Cell and initialize it.</span>
 <span class="sd">:param batch_size: Size of batches</span>
 <span class="sd">:param rnn_size: Size of RNNs</span>
 <span class="sd">:return: Tuple (cell, initialize state)</span>
 <span class="sd">"""</span>
    <span class="n">lstm</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">contrib</span><span class="o">.</span><span class="n">rnn</span><span class="o">.</span><span class="n">BasicLSTMCell</span><span class="p">(</span><span class="n">rnn_size</span><span class="p">)</span>
    <span class="c1"># Stack up multiple LSTM layers, for deep learning</span>
    <span class="c1">#drop = tf.contrib.rnn.DropoutWrapper(lstm, output_keep_prob=0.5)</span>
    <span class="n">cell</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">contrib</span><span class="o">.</span><span class="n">rnn</span><span class="o">.</span><span class="n">MultiRNNCell</span><span class="p">([</span><span class="n">lstm</span><span class="p">]</span><span class="o">*</span><span class="mi">2</span><span class="p">)</span>
    <span class="c1"># Getting an initial state of all zeros</span>

    <span class="n">initial_state</span> <span class="o">=</span> <span class="n">cell</span><span class="o">.</span><span class="n">zero_state</span><span class="p">(</span><span class="n">batch_size</span><span class="p">,</span> <span class="n">tf</span><span class="o">.</span><span class="n">float32</span><span class="p">)</span>
    <span class="n">initial_state</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">identity</span><span class="p">(</span><span class="n">initial_state</span><span class="p">,</span><span class="n">name</span><span class="o">=</span><span class="s2">"initial_state"</span><span class="p">)</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">cell</span><span class="p">,</span> <span class="n">initial_state</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_get_init_cell</span><span class="p">(</span><span class="n">get_init_cell</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Word Embedding[¶](#Word-Embedding)

Apply embedding to `input_data` using TensorFlow. Return the embedded sequence.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [50]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">get_embed</span><span class="p">(</span><span class="n">input_data</span><span class="p">,</span> <span class="n">vocab_size</span><span class="p">,</span> <span class="n">embed_dim</span><span class="p">):</span>
    <span class="n">embedding</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">Variable</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">random_uniform</span><span class="p">((</span><span class="n">vocab_size</span><span class="p">,</span> <span class="n">embed_dim</span><span class="p">),</span> <span class="o">-</span><span class="mi">1</span><span class="p">,</span> <span class="mi">1</span><span class="p">))</span>
    <span class="n">embed</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">nn</span><span class="o">.</span><span class="n">embedding_lookup</span><span class="p">(</span><span class="n">embedding</span><span class="p">,</span> <span class="n">input_data</span><span class="p">)</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">embed</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_get_embed</span><span class="p">(</span><span class="n">get_embed</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Build RNN[¶](#Build-RNN)

You created a RNN Cell in the `get_init_cell()` function. Time to use the cell to create a RNN.

*   Build the RNN using the [`tf.nn.dynamic_rnn()`](https://www.tensorflow.org/api_docs/python/tf/nn/dynamic_rnn)
    *   Apply the name "final_state" to the final state using [`tf.identity()`](https://www.tensorflow.org/api_docs/python/tf/identity)

Return the outputs and final_state state in the following tuple `(Outputs, FinalState)`

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [51]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">build_rnn</span><span class="p">(</span><span class="n">cell</span><span class="p">,</span> <span class="n">inputs</span><span class="p">):</span>
    <span class="sd">"""</span>
 <span class="sd">Create a RNN using a RNN Cell</span>
 <span class="sd">:param cell: RNN Cell</span>
 <span class="sd">:param inputs: Input text data</span>
 <span class="sd">:return: Tuple (Outputs, Final State)</span>
 <span class="sd">"""</span>
    <span class="c1">#tensor, initial = get_init_cell(cell,inputs)</span>
    <span class="n">outputs</span><span class="p">,</span> <span class="n">state</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">nn</span><span class="o">.</span><span class="n">dynamic_rnn</span><span class="p">(</span><span class="n">cell</span><span class="p">,</span> <span class="n">inputs</span><span class="p">,</span><span class="n">dtype</span><span class="o">=</span><span class="n">tf</span><span class="o">.</span><span class="n">float32</span><span class="p">)</span>
    <span class="n">final_state</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">identity</span><span class="p">(</span><span class="n">state</span><span class="p">,</span><span class="n">name</span> <span class="o">=</span> <span class="s2">"final_state"</span><span class="p">)</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">outputs</span><span class="p">,</span> <span class="n">final_state</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_build_rnn</span><span class="p">(</span><span class="n">build_rnn</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Build the Neural Network[¶](#Build-the-Neural-Network)

Apply the functions you implemented above to:

*   Apply embedding to `input_data` using your `get_embed(input_data, vocab_size, embed_dim)` function.
*   Build RNN using `cell` and your `build_rnn(cell, inputs)` function.
*   Apply a fully connected layer with a linear activation and `vocab_size` as the number of outputs.

Return the logits and final state in the following tuple (Logits, FinalState)

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [64]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">build_nn</span><span class="p">(</span><span class="n">cell</span><span class="p">,</span> <span class="n">rnn_size</span><span class="p">,</span> <span class="n">input_data</span><span class="p">,</span> <span class="n">vocab_size</span><span class="p">,</span> <span class="n">embed_dim</span><span class="p">):</span>
    <span class="sd">"""</span>
 <span class="sd">Build part of the neural network</span>
 <span class="sd">:param cell: RNN cell</span>
 <span class="sd">:param rnn_size: Size of rnns</span>
 <span class="sd">:param input_data: Input data</span>
 <span class="sd">:param vocab_size: Vocabulary size</span>
 <span class="sd">:param embed_dim: Number of embedding dimensions</span>
 <span class="sd">:return: Tuple (Logits, FinalState)</span>
 <span class="sd">"""</span>
    <span class="n">input_data</span> <span class="o">=</span> <span class="n">get_embed</span><span class="p">(</span><span class="n">input_data</span><span class="p">,</span> <span class="n">vocab_size</span><span class="p">,</span> <span class="n">embed_dim</span><span class="p">)</span>
    <span class="n">outputs</span><span class="p">,</span> <span class="n">FinalState</span> <span class="o">=</span> <span class="n">build_rnn</span><span class="p">(</span><span class="n">cell</span><span class="p">,</span> <span class="n">input_data</span><span class="p">)</span>
    <span class="n">Logits</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">contrib</span><span class="o">.</span><span class="n">layers</span><span class="o">.</span><span class="n">fully_connected</span><span class="p">(</span><span class="n">outputs</span><span class="p">,</span><span class="n">vocab_size</span><span class="p">,</span><span class="n">activation_fn</span><span class="o">=</span><span class="kc">None</span><span class="p">,</span> <span class="n">weights_initializer</span><span class="o">=</span><span class="n">tf</span><span class="o">.</span><span class="n">truncated_normal_initializer</span><span class="p">(</span><span class="n">stddev</span><span class="o">=</span><span class="mf">1.0</span><span class="p">))</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">Logits</span><span class="p">,</span> <span class="n">FinalState</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_build_nn</span><span class="p">(</span><span class="n">build_nn</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Batches[¶](#Batches)

Implement `get_batches` to create batches of input and targets using `int_text`. The batches should be a Numpy array with the shape `(number of batches, 2, batch size, sequence length)`. Each batch contains two elements:

*   The first element is a single batch of **input** with the shape `[batch size, sequence length]`
*   The second element is a single batch of **targets** with the shape `[batch size, sequence length]`

If you can't fill the last batch with enough data, drop the last batch.

For exmple, `get_batches([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20], 3, 2)` would return a Numpy array of the following:

    [
      # First Batch
      [
        # Batch of Input
        [[ 1  2], [ 7  8], [13 14]]
        # Batch of targets
        [[ 2  3], [ 8  9], [14 15]]
      ]

      # Second Batch
      [
        # Batch of Input
        [[ 3  4], [ 9 10], [15 16]]
        # Batch of targets
        [[ 4  5], [10 11], [16 17]]
      ]

      # Third Batch
      [
        # Batch of Input
        [[ 5  6], [11 12], [17 18]]
        # Batch of targets
        [[ 6  7], [12 13], [18  1]]
      ]
    ]

Notice that the last target value in the last batch is the first input value of the first batch. In this case, `1`. This is a common technique used when creating sequence batches, although it is rather unintuitive.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [53]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">get_batches</span><span class="p">(</span><span class="n">int_text</span><span class="p">,</span> <span class="n">batch_size</span><span class="p">,</span> <span class="n">seq_length</span><span class="p">):</span>
<span class="c1">#batch_size = 3</span>
<span class="c1">#seq_length = 2</span>
<span class="c1">#int_text = list(range(1000*seq_length))</span>
    <span class="sd">"""</span>
 <span class="sd">Return batches of input and target</span>
 <span class="sd">:param int_text: Text with the words replaced by their ids</span>
 <span class="sd">:param batch_size: The size of batch</span>
 <span class="sd">:param seq_length: The length of sequence</span>
 <span class="sd">:return: Batches as a Numpy array</span>
 <span class="sd">"""</span>
    <span class="n">num_batches</span> <span class="o">=</span> <span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">int_text</span><span class="p">)</span><span class="o">//</span><span class="n">batch_size</span><span class="o">//</span><span class="n">seq_length</span><span class="p">)</span>
    <span class="c1">#print(int_text)</span>
    <span class="n">batches</span> <span class="o">=</span> <span class="n">np</span><span class="o">.</span><span class="n">zeros</span><span class="p">((</span><span class="n">num_batches</span><span class="p">,</span><span class="mi">2</span><span class="p">,</span><span class="n">batch_size</span><span class="p">,</span><span class="n">seq_length</span><span class="p">))</span>
    <span class="c1">#print(num_batches)</span>
    <span class="c1">#print(batch_size)</span>
    <span class="c1">#print(seq_length)</span>
    <span class="k">for</span> <span class="n">f</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">0</span><span class="p">,</span><span class="n">num_batches</span><span class="p">):</span>
        <span class="k">for</span> <span class="n">x</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">0</span><span class="p">,</span><span class="n">batch_size</span><span class="p">):</span>
            <span class="k">for</span> <span class="n">y</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">0</span><span class="p">,</span><span class="n">seq_length</span><span class="p">):</span>
                <span class="n">g</span><span class="o">=</span><span class="n">y</span><span class="o">+</span><span class="n">x</span><span class="o">*</span><span class="n">num_batches</span><span class="o">*</span><span class="n">seq_length</span>
                <span class="n">batches</span><span class="p">[</span><span class="n">f</span><span class="p">][</span><span class="mi">0</span><span class="p">][</span><span class="n">x</span><span class="p">][</span><span class="n">y</span><span class="p">]</span> <span class="o">=</span> <span class="n">int_text</span><span class="p">[</span><span class="n">g</span><span class="o">+</span><span class="n">f</span><span class="o">*</span><span class="n">seq_length</span><span class="p">]</span>
                <span class="n">batches</span><span class="p">[</span><span class="n">f</span><span class="p">][</span><span class="mi">1</span><span class="p">][</span><span class="n">x</span><span class="p">][</span><span class="n">y</span><span class="p">]</span> <span class="o">=</span> <span class="n">int_text</span><span class="p">[</span><span class="n">g</span><span class="o">+</span><span class="mi">1</span><span class="o">+</span><span class="n">f</span><span class="o">*</span><span class="n">seq_length</span><span class="p">]</span>
                <span class="k">if</span> <span class="p">(</span><span class="n">f</span><span class="o">==</span><span class="n">num_batches</span><span class="o">-</span><span class="mi">1</span> <span class="ow">and</span> <span class="n">x</span><span class="o">==</span><span class="n">batch_size</span><span class="o">-</span><span class="mi">1</span> <span class="ow">and</span> <span class="n">y</span><span class="o">==</span><span class="n">seq_length</span><span class="o">-</span><span class="mi">1</span><span class="p">):</span>
                    <span class="n">batches</span><span class="p">[</span><span class="n">f</span><span class="p">][</span><span class="mi">1</span><span class="p">][</span><span class="n">x</span><span class="p">][</span><span class="n">y</span><span class="p">]</span> <span class="o">=</span> <span class="n">int_text</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span>

    <span class="k">return</span> <span class="n">batches</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_get_batches</span><span class="p">(</span><span class="n">get_batches</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Neural Network Training[¶](#Neural-Network-Training)

### Hyperparameters[¶](#Hyperparameters)

Tune the following parameters:

*   Set `num_epochs` to the number of epochs.
*   Set `batch_size` to the batch size.
*   Set `rnn_size` to the size of the RNNs.
*   Set `embed_dim` to the size of the embedding.
*   Set `seq_length` to the length of sequence.
*   Set `learning_rate` to the learning rate.
*   Set `show_every_n_batches` to the number of batches the neural network should print progress.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [71]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="c1"># Number of Epochs</span>
<span class="n">num_epochs</span> <span class="o">=</span> <span class="mi">60</span>
<span class="c1"># Batch Size</span>
<span class="n">batch_size</span> <span class="o">=</span> <span class="mi">128</span>
<span class="c1"># RNN Size</span>
<span class="n">rnn_size</span> <span class="o">=</span> <span class="mi">1024</span>
<span class="c1"># Embedding Dimension Size</span>
<span class="n">embed_dim</span> <span class="o">=</span> <span class="mi">256</span>
<span class="c1"># Sequence Length</span>
<span class="n">seq_length</span> <span class="o">=</span> <span class="mi">20</span>
<span class="c1"># Learning Rate</span>
<span class="n">learning_rate</span> <span class="o">=</span> <span class="mf">0.001</span>
<span class="c1"># Show stats for every n number of batches</span>
<span class="n">show_every_n_batches</span> <span class="o">=</span> <span class="mi">1</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">save_dir</span> <span class="o">=</span> <span class="s1">'./save'</span>
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Build the Graph[¶](#Build-the-Graph)

Build the graph using the neural network you implemented.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [72]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">from</span> <span class="nn">tensorflow.contrib</span> <span class="k">import</span> <span class="n">seq2seq</span>

<span class="n">train_graph</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">Graph</span><span class="p">()</span>
<span class="k">with</span> <span class="n">train_graph</span><span class="o">.</span><span class="n">as_default</span><span class="p">():</span>
    <span class="n">vocab_size</span> <span class="o">=</span> <span class="nb">len</span><span class="p">(</span><span class="n">int_to_vocab</span><span class="p">)</span>
    <span class="n">input_text</span><span class="p">,</span> <span class="n">targets</span><span class="p">,</span> <span class="n">lr</span> <span class="o">=</span> <span class="n">get_inputs</span><span class="p">()</span>
    <span class="n">input_data_shape</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">shape</span><span class="p">(</span><span class="n">input_text</span><span class="p">)</span>
    <span class="n">cell</span><span class="p">,</span> <span class="n">initial_state</span> <span class="o">=</span> <span class="n">get_init_cell</span><span class="p">(</span><span class="n">input_data_shape</span><span class="p">[</span><span class="mi">0</span><span class="p">],</span> <span class="n">rnn_size</span><span class="p">)</span>
    <span class="n">logits</span><span class="p">,</span> <span class="n">final_state</span> <span class="o">=</span> <span class="n">build_nn</span><span class="p">(</span><span class="n">cell</span><span class="p">,</span> <span class="n">rnn_size</span><span class="p">,</span> <span class="n">input_text</span><span class="p">,</span> <span class="n">vocab_size</span><span class="p">,</span> <span class="n">embed_dim</span><span class="p">)</span>
    <span class="c1"># Probabilities for generating words</span>
    <span class="n">probs</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">nn</span><span class="o">.</span><span class="n">softmax</span><span class="p">(</span><span class="n">logits</span><span class="p">,</span> <span class="n">name</span><span class="o">=</span><span class="s1">'probs'</span><span class="p">)</span>

    <span class="c1"># Loss function</span>
    <span class="n">cost</span> <span class="o">=</span> <span class="n">seq2seq</span><span class="o">.</span><span class="n">sequence_loss</span><span class="p">(</span>
        <span class="n">logits</span><span class="p">,</span>
        <span class="n">targets</span><span class="p">,</span>
        <span class="n">tf</span><span class="o">.</span><span class="n">ones</span><span class="p">([</span><span class="n">input_data_shape</span><span class="p">[</span><span class="mi">0</span><span class="p">],</span> <span class="n">input_data_shape</span><span class="p">[</span><span class="mi">1</span><span class="p">]]))</span>

    <span class="c1"># Optimizer</span>
    <span class="n">optimizer</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">train</span><span class="o">.</span><span class="n">AdamOptimizer</span><span class="p">(</span><span class="n">lr</span><span class="p">)</span>

    <span class="c1"># Gradient Clipping</span>
    <span class="n">gradients</span> <span class="o">=</span> <span class="n">optimizer</span><span class="o">.</span><span class="n">compute_gradients</span><span class="p">(</span><span class="n">cost</span><span class="p">)</span>
    <span class="n">capped_gradients</span> <span class="o">=</span> <span class="p">[(</span><span class="n">tf</span><span class="o">.</span><span class="n">clip_by_value</span><span class="p">(</span><span class="n">grad</span><span class="p">,</span> <span class="o">-</span><span class="mf">1.</span><span class="p">,</span> <span class="mf">1.</span><span class="p">),</span> <span class="n">var</span><span class="p">)</span> <span class="k">for</span> <span class="n">grad</span><span class="p">,</span> <span class="n">var</span> <span class="ow">in</span> <span class="n">gradients</span> <span class="k">if</span> <span class="n">grad</span> <span class="ow">is</span> <span class="ow">not</span> <span class="kc">None</span><span class="p">]</span>
    <span class="n">train_op</span> <span class="o">=</span> <span class="n">optimizer</span><span class="o">.</span><span class="n">apply_gradients</span><span class="p">(</span><span class="n">capped_gradients</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Train[¶](#Train)

Train the neural network on the preprocessed data. If you have a hard time getting a good loss, check the [forums](https://discussions.udacity.com/) to see if anyone is having the same problem.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [73]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="n">batches</span> <span class="o">=</span> <span class="n">get_batches</span><span class="p">(</span><span class="n">int_text</span><span class="p">,</span> <span class="n">batch_size</span><span class="p">,</span> <span class="n">seq_length</span><span class="p">)</span>

<span class="k">with</span> <span class="n">tf</span><span class="o">.</span><span class="n">Session</span><span class="p">(</span><span class="n">graph</span><span class="o">=</span><span class="n">train_graph</span><span class="p">)</span> <span class="k">as</span> <span class="n">sess</span><span class="p">:</span>
    <span class="n">sess</span><span class="o">.</span><span class="n">run</span><span class="p">(</span><span class="n">tf</span><span class="o">.</span><span class="n">global_variables_initializer</span><span class="p">())</span>

    <span class="k">for</span> <span class="n">epoch_i</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="n">num_epochs</span><span class="p">):</span>
        <span class="n">state</span> <span class="o">=</span> <span class="n">sess</span><span class="o">.</span><span class="n">run</span><span class="p">(</span><span class="n">initial_state</span><span class="p">,</span> <span class="p">{</span><span class="n">input_text</span><span class="p">:</span> <span class="n">batches</span><span class="p">[</span><span class="mi">0</span><span class="p">][</span><span class="mi">0</span><span class="p">]})</span>

        <span class="k">for</span> <span class="n">batch_i</span><span class="p">,</span> <span class="p">(</span><span class="n">x</span><span class="p">,</span> <span class="n">y</span><span class="p">)</span> <span class="ow">in</span> <span class="nb">enumerate</span><span class="p">(</span><span class="n">batches</span><span class="p">):</span>
            <span class="n">feed</span> <span class="o">=</span> <span class="p">{</span>
                <span class="n">input_text</span><span class="p">:</span> <span class="n">x</span><span class="p">,</span>
                <span class="n">targets</span><span class="p">:</span> <span class="n">y</span><span class="p">,</span>
                <span class="n">initial_state</span><span class="p">:</span> <span class="n">state</span><span class="p">,</span>
                <span class="n">lr</span><span class="p">:</span> <span class="n">learning_rate</span><span class="p">}</span>
            <span class="n">train_loss</span><span class="p">,</span> <span class="n">state</span><span class="p">,</span> <span class="n">_</span> <span class="o">=</span> <span class="n">sess</span><span class="o">.</span><span class="n">run</span><span class="p">([</span><span class="n">cost</span><span class="p">,</span> <span class="n">final_state</span><span class="p">,</span> <span class="n">train_op</span><span class="p">],</span> <span class="n">feed</span><span class="p">)</span>

            <span class="c1"># Show every <show_every_n_batches> batches</span>
            <span class="k">if</span> <span class="p">(</span><span class="n">epoch_i</span> <span class="o">*</span> <span class="nb">len</span><span class="p">(</span><span class="n">batches</span><span class="p">)</span> <span class="o">+</span> <span class="n">batch_i</span><span class="p">)</span> <span class="o">%</span> <span class="n">show_every_n_batches</span> <span class="o">==</span> <span class="mi">0</span><span class="p">:</span>
                <span class="nb">print</span><span class="p">(</span><span class="s1">'Epoch</span> <span class="si">{:>3}</span> <span class="s1">Batch</span> <span class="si">{:>4}</span><span class="s1">/</span><span class="si">{}</span> <span class="s1">train_loss =</span> <span class="si">{:.3f}</span><span class="s1">'</span><span class="o">.</span><span class="n">format</span><span class="p">(</span>
                    <span class="n">epoch_i</span><span class="p">,</span>
                    <span class="n">batch_i</span><span class="p">,</span>
                    <span class="nb">len</span><span class="p">(</span><span class="n">batches</span><span class="p">),</span>
                    <span class="n">train_loss</span><span class="p">))</span>

    <span class="c1"># Save Model</span>
    <span class="n">saver</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">train</span><span class="o">.</span><span class="n">Saver</span><span class="p">()</span>
    <span class="n">saver</span><span class="o">.</span><span class="n">save</span><span class="p">(</span><span class="n">sess</span><span class="p">,</span> <span class="n">save_dir</span><span class="p">)</span>
    <span class="nb">print</span><span class="p">(</span><span class="s1">'Model Trained and Saved'</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Epoch   0 Batch    0/26   train_loss = 9.101
Epoch   0 Batch    1/26   train_loss = 11.052
Epoch   0 Batch    2/26   train_loss = 9.057
Epoch   0 Batch    3/26   train_loss = 9.078
Epoch   0 Batch    4/26   train_loss = 8.411
Epoch   0 Batch    5/26   train_loss = 8.272
Epoch   0 Batch    6/26   train_loss = 8.045
Epoch   0 Batch    7/26   train_loss = 7.715
Epoch   0 Batch    8/26   train_loss = 7.775
Epoch   0 Batch    9/26   train_loss = 7.658
Epoch   0 Batch   10/26   train_loss = 7.366
Epoch   0 Batch   11/26   train_loss = 7.071
Epoch   0 Batch   12/26   train_loss = 7.099
Epoch   0 Batch   13/26   train_loss = 6.979
Epoch   0 Batch   14/26   train_loss = 6.824
Epoch   0 Batch   15/26   train_loss = 6.703
Epoch   0 Batch   16/26   train_loss = 6.812
Epoch   0 Batch   17/26   train_loss = 6.680
Epoch   0 Batch   18/26   train_loss = 6.639
Epoch   0 Batch   19/26   train_loss = 6.532
Epoch   0 Batch   20/26   train_loss = 6.483
Epoch   0 Batch   21/26   train_loss = 6.339
Epoch   0 Batch   22/26   train_loss = 6.264
Epoch   0 Batch   23/26   train_loss = 6.430
Epoch   0 Batch   24/26   train_loss = 6.309
Epoch   0 Batch   25/26   train_loss = 6.234
Epoch   1 Batch    0/26   train_loss = 5.795
Epoch   1 Batch    1/26   train_loss = 5.967
Epoch   1 Batch    2/26   train_loss = 5.852
Epoch   1 Batch    3/26   train_loss = 5.878
Epoch   1 Batch    4/26   train_loss = 5.776
Epoch   1 Batch    5/26   train_loss = 5.753
Epoch   1 Batch    6/26   train_loss = 5.865
Epoch   1 Batch    7/26   train_loss = 5.837
Epoch   1 Batch    8/26   train_loss = 5.697
Epoch   1 Batch    9/26   train_loss = 5.765
Epoch   1 Batch   10/26   train_loss = 5.687
Epoch   1 Batch   11/26   train_loss = 5.601
Epoch   1 Batch   12/26   train_loss = 5.820
Epoch   1 Batch   13/26   train_loss = 5.653
Epoch   1 Batch   14/26   train_loss = 5.537
Epoch   1 Batch   15/26   train_loss = 5.451
Epoch   1 Batch   16/26   train_loss = 5.581
Epoch   1 Batch   17/26   train_loss = 5.573
Epoch   1 Batch   18/26   train_loss = 5.582
Epoch   1 Batch   19/26   train_loss = 5.574
Epoch   1 Batch   20/26   train_loss = 5.541
Epoch   1 Batch   21/26   train_loss = 5.455
Epoch   1 Batch   22/26   train_loss = 5.461
Epoch   1 Batch   23/26   train_loss = 5.659
Epoch   1 Batch   24/26   train_loss = 5.560
Epoch   1 Batch   25/26   train_loss = 5.520
Epoch   2 Batch    0/26   train_loss = 5.195
Epoch   2 Batch    1/26   train_loss = 5.381
Epoch   2 Batch    2/26   train_loss = 5.244
Epoch   2 Batch    3/26   train_loss = 5.312
Epoch   2 Batch    4/26   train_loss = 5.206
Epoch   2 Batch    5/26   train_loss = 5.187
Epoch   2 Batch    6/26   train_loss = 5.317
Epoch   2 Batch    7/26   train_loss = 5.313
Epoch   2 Batch    8/26   train_loss = 5.186
Epoch   2 Batch    9/26   train_loss = 5.298
Epoch   2 Batch   10/26   train_loss = 5.213
Epoch   2 Batch   11/26   train_loss = 5.144
Epoch   2 Batch   12/26   train_loss = 5.348
Epoch   2 Batch   13/26   train_loss = 5.217
Epoch   2 Batch   14/26   train_loss = 5.093
Epoch   2 Batch   15/26   train_loss = 4.996
Epoch   2 Batch   16/26   train_loss = 5.139
Epoch   2 Batch   17/26   train_loss = 5.161
Epoch   2 Batch   18/26   train_loss = 5.184
Epoch   2 Batch   19/26   train_loss = 5.178
Epoch   2 Batch   20/26   train_loss = 5.116
Epoch   2 Batch   21/26   train_loss = 5.047
Epoch   2 Batch   22/26   train_loss = 5.074
Epoch   2 Batch   23/26   train_loss = 5.256
Epoch   2 Batch   24/26   train_loss = 5.156
Epoch   2 Batch   25/26   train_loss = 5.092
Epoch   3 Batch    0/26   train_loss = 4.760
Epoch   3 Batch    1/26   train_loss = 4.985
Epoch   3 Batch    2/26   train_loss = 4.855
Epoch   3 Batch    3/26   train_loss = 4.930
Epoch   3 Batch    4/26   train_loss = 4.837
Epoch   3 Batch    5/26   train_loss = 4.827
Epoch   3 Batch    6/26   train_loss = 4.948
Epoch   3 Batch    7/26   train_loss = 4.954
Epoch   3 Batch    8/26   train_loss = 4.816
Epoch   3 Batch    9/26   train_loss = 4.934
Epoch   3 Batch   10/26   train_loss = 4.851
Epoch   3 Batch   11/26   train_loss = 4.788
Epoch   3 Batch   12/26   train_loss = 4.974
Epoch   3 Batch   13/26   train_loss = 4.848
Epoch   3 Batch   14/26   train_loss = 4.716
Epoch   3 Batch   15/26   train_loss = 4.649
Epoch   3 Batch   16/26   train_loss = 4.767
Epoch   3 Batch   17/26   train_loss = 4.793
Epoch   3 Batch   18/26   train_loss = 4.832
Epoch   3 Batch   19/26   train_loss = 4.818
Epoch   3 Batch   20/26   train_loss = 4.732
Epoch   3 Batch   21/26   train_loss = 4.676
Epoch   3 Batch   22/26   train_loss = 4.718
Epoch   3 Batch   23/26   train_loss = 4.866
Epoch   3 Batch   24/26   train_loss = 4.770
Epoch   3 Batch   25/26   train_loss = 4.717
Epoch   4 Batch    0/26   train_loss = 4.329
Epoch   4 Batch    1/26   train_loss = 4.595
Epoch   4 Batch    2/26   train_loss = 4.467
Epoch   4 Batch    3/26   train_loss = 4.546
Epoch   4 Batch    4/26   train_loss = 4.462
Epoch   4 Batch    5/26   train_loss = 4.446
Epoch   4 Batch    6/26   train_loss = 4.555
Epoch   4 Batch    7/26   train_loss = 4.563
Epoch   4 Batch    8/26   train_loss = 4.405
Epoch   4 Batch    9/26   train_loss = 4.553
Epoch   4 Batch   10/26   train_loss = 4.456
Epoch   4 Batch   11/26   train_loss = 4.407
Epoch   4 Batch   12/26   train_loss = 4.562
Epoch   4 Batch   13/26   train_loss = 4.436
Epoch   4 Batch   14/26   train_loss = 4.309
Epoch   4 Batch   15/26   train_loss = 4.260
Epoch   4 Batch   16/26   train_loss = 4.344
Epoch   4 Batch   17/26   train_loss = 4.351
Epoch   4 Batch   18/26   train_loss = 4.443
Epoch   4 Batch   19/26   train_loss = 4.409
Epoch   4 Batch   20/26   train_loss = 4.288
Epoch   4 Batch   21/26   train_loss = 4.248
Epoch   4 Batch   22/26   train_loss = 4.296
Epoch   4 Batch   23/26   train_loss = 4.419
Epoch   4 Batch   24/26   train_loss = 4.326
Epoch   4 Batch   25/26   train_loss = 4.298
Epoch   5 Batch    0/26   train_loss = 3.858
Epoch   5 Batch    1/26   train_loss = 4.121
Epoch   5 Batch    2/26   train_loss = 4.026
Epoch   5 Batch    3/26   train_loss = 4.079
Epoch   5 Batch    4/26   train_loss = 3.989
Epoch   5 Batch    5/26   train_loss = 3.992
Epoch   5 Batch    6/26   train_loss = 4.042
Epoch   5 Batch    7/26   train_loss = 4.042
Epoch   5 Batch    8/26   train_loss = 3.911
Epoch   5 Batch    9/26   train_loss = 4.019
Epoch   5 Batch   10/26   train_loss = 3.958
Epoch   5 Batch   11/26   train_loss = 3.894
Epoch   5 Batch   12/26   train_loss = 3.961
Epoch   5 Batch   13/26   train_loss = 3.892
Epoch   5 Batch   14/26   train_loss = 3.779
Epoch   5 Batch   15/26   train_loss = 3.718
Epoch   5 Batch   16/26   train_loss = 3.825
Epoch   5 Batch   17/26   train_loss = 3.813
Epoch   5 Batch   18/26   train_loss = 3.874
Epoch   5 Batch   19/26   train_loss = 3.855
Epoch   5 Batch   20/26   train_loss = 3.743
Epoch   5 Batch   21/26   train_loss = 3.736
Epoch   5 Batch   22/26   train_loss = 3.732
Epoch   5 Batch   23/26   train_loss = 3.831
Epoch   5 Batch   24/26   train_loss = 3.734
Epoch   5 Batch   25/26   train_loss = 3.712
Epoch   6 Batch    0/26   train_loss = 3.308
Epoch   6 Batch    1/26   train_loss = 3.559
Epoch   6 Batch    2/26   train_loss = 3.483
Epoch   6 Batch    3/26   train_loss = 3.482
Epoch   6 Batch    4/26   train_loss = 3.482
Epoch   6 Batch    5/26   train_loss = 3.423
Epoch   6 Batch    6/26   train_loss = 3.412
Epoch   6 Batch    7/26   train_loss = 3.419
Epoch   6 Batch    8/26   train_loss = 3.333
Epoch   6 Batch    9/26   train_loss = 3.375
Epoch   6 Batch   10/26   train_loss = 3.377
Epoch   6 Batch   11/26   train_loss = 3.262
Epoch   6 Batch   12/26   train_loss = 3.303
Epoch   6 Batch   13/26   train_loss = 3.289
Epoch   6 Batch   14/26   train_loss = 3.152
Epoch   6 Batch   15/26   train_loss = 3.049
Epoch   6 Batch   16/26   train_loss = 3.247
Epoch   6 Batch   17/26   train_loss = 3.218
Epoch   6 Batch   18/26   train_loss = 3.312
Epoch   6 Batch   19/26   train_loss = 3.271
Epoch   6 Batch   20/26   train_loss = 3.173
Epoch   6 Batch   21/26   train_loss = 3.222
Epoch   6 Batch   22/26   train_loss = 3.179
Epoch   6 Batch   23/26   train_loss = 3.196
Epoch   6 Batch   24/26   train_loss = 3.210
Epoch   6 Batch   25/26   train_loss = 3.143
Epoch   7 Batch    0/26   train_loss = 2.865
Epoch   7 Batch    1/26   train_loss = 3.140
Epoch   7 Batch    2/26   train_loss = 2.935
Epoch   7 Batch    3/26   train_loss = 3.030
Epoch   7 Batch    4/26   train_loss = 2.949
Epoch   7 Batch    5/26   train_loss = 2.965
Epoch   7 Batch    6/26   train_loss = 2.977
Epoch   7 Batch    7/26   train_loss = 2.943
Epoch   7 Batch    8/26   train_loss = 2.928
Epoch   7 Batch    9/26   train_loss = 3.028
Epoch   7 Batch   10/26   train_loss = 2.834
Epoch   7 Batch   11/26   train_loss = 2.836
Epoch   7 Batch   12/26   train_loss = 2.849
Epoch   7 Batch   13/26   train_loss = 2.853
Epoch   7 Batch   14/26   train_loss = 2.770
Epoch   7 Batch   15/26   train_loss = 2.671
Epoch   7 Batch   16/26   train_loss = 2.692
Epoch   7 Batch   17/26   train_loss = 2.782
Epoch   7 Batch   18/26   train_loss = 2.857
Epoch   7 Batch   19/26   train_loss = 2.731
Epoch   7 Batch   20/26   train_loss = 2.713
Epoch   7 Batch   21/26   train_loss = 2.759
Epoch   7 Batch   22/26   train_loss = 2.676
Epoch   7 Batch   23/26   train_loss = 2.703
Epoch   7 Batch   24/26   train_loss = 2.807
Epoch   7 Batch   25/26   train_loss = 2.731
Epoch   8 Batch    0/26   train_loss = 2.446
Epoch   8 Batch    1/26   train_loss = 2.644
Epoch   8 Batch    2/26   train_loss = 2.623
Epoch   8 Batch    3/26   train_loss = 2.613
Epoch   8 Batch    4/26   train_loss = 2.559
Epoch   8 Batch    5/26   train_loss = 2.544
Epoch   8 Batch    6/26   train_loss = 2.574
Epoch   8 Batch    7/26   train_loss = 2.549
Epoch   8 Batch    8/26   train_loss = 2.491
Epoch   8 Batch    9/26   train_loss = 2.616
Epoch   8 Batch   10/26   train_loss = 2.458
Epoch   8 Batch   11/26   train_loss = 2.440
Epoch   8 Batch   12/26   train_loss = 2.413
Epoch   8 Batch   13/26   train_loss = 2.358
Epoch   8 Batch   14/26   train_loss = 2.362
Epoch   8 Batch   15/26   train_loss = 2.295
Epoch   8 Batch   16/26   train_loss = 2.283
Epoch   8 Batch   17/26   train_loss = 2.260
Epoch   8 Batch   18/26   train_loss = 2.270
Epoch   8 Batch   19/26   train_loss = 2.226
Epoch   8 Batch   20/26   train_loss = 2.213
Epoch   8 Batch   21/26   train_loss = 2.206
Epoch   8 Batch   22/26   train_loss = 2.136
Epoch   8 Batch   23/26   train_loss = 2.141
Epoch   8 Batch   24/26   train_loss = 2.326
Epoch   8 Batch   25/26   train_loss = 2.118
Epoch   9 Batch    0/26   train_loss = 1.970
Epoch   9 Batch    1/26   train_loss = 2.120
Epoch   9 Batch    2/26   train_loss = 2.048
Epoch   9 Batch    3/26   train_loss = 2.063
Epoch   9 Batch    4/26   train_loss = 2.095
Epoch   9 Batch    5/26   train_loss = 1.963
Epoch   9 Batch    6/26   train_loss = 2.027
Epoch   9 Batch    7/26   train_loss = 1.961
Epoch   9 Batch    8/26   train_loss = 1.952
Epoch   9 Batch    9/26   train_loss = 2.094
Epoch   9 Batch   10/26   train_loss = 1.925
Epoch   9 Batch   11/26   train_loss = 1.988
Epoch   9 Batch   12/26   train_loss = 1.961
Epoch   9 Batch   13/26   train_loss = 1.881
Epoch   9 Batch   14/26   train_loss = 1.879
Epoch   9 Batch   15/26   train_loss = 1.889
Epoch   9 Batch   16/26   train_loss = 1.764
Epoch   9 Batch   17/26   train_loss = 1.849
Epoch   9 Batch   18/26   train_loss = 1.897
Epoch   9 Batch   19/26   train_loss = 1.859
Epoch   9 Batch   20/26   train_loss = 1.819
Epoch   9 Batch   21/26   train_loss = 1.810
Epoch   9 Batch   22/26   train_loss = 1.768
Epoch   9 Batch   23/26   train_loss = 1.807
Epoch   9 Batch   24/26   train_loss = 1.872
Epoch   9 Batch   25/26   train_loss = 1.768
Epoch  10 Batch    0/26   train_loss = 1.687
Epoch  10 Batch    1/26   train_loss = 1.768
Epoch  10 Batch    2/26   train_loss = 1.753
Epoch  10 Batch    3/26   train_loss = 1.745
Epoch  10 Batch    4/26   train_loss = 1.654
Epoch  10 Batch    5/26   train_loss = 1.660
Epoch  10 Batch    6/26   train_loss = 1.720
Epoch  10 Batch    7/26   train_loss = 1.626
Epoch  10 Batch    8/26   train_loss = 1.618
Epoch  10 Batch    9/26   train_loss = 1.697
Epoch  10 Batch   10/26   train_loss = 1.621
Epoch  10 Batch   11/26   train_loss = 1.603
Epoch  10 Batch   12/26   train_loss = 1.583
Epoch  10 Batch   13/26   train_loss = 1.547
Epoch  10 Batch   14/26   train_loss = 1.590
Epoch  10 Batch   15/26   train_loss = 1.601
Epoch  10 Batch   16/26   train_loss = 1.537
Epoch  10 Batch   17/26   train_loss = 1.504
Epoch  10 Batch   18/26   train_loss = 1.524
Epoch  10 Batch   19/26   train_loss = 1.540
Epoch  10 Batch   20/26   train_loss = 1.473
Epoch  10 Batch   21/26   train_loss = 1.470
Epoch  10 Batch   22/26   train_loss = 1.429
Epoch  10 Batch   23/26   train_loss = 1.573
Epoch  10 Batch   24/26   train_loss = 1.629
Epoch  10 Batch   25/26   train_loss = 1.420
Epoch  11 Batch    0/26   train_loss = 1.329
Epoch  11 Batch    1/26   train_loss = 1.449
Epoch  11 Batch    2/26   train_loss = 1.444
Epoch  11 Batch    3/26   train_loss = 1.470
Epoch  11 Batch    4/26   train_loss = 1.373
Epoch  11 Batch    5/26   train_loss = 1.435
Epoch  11 Batch    6/26   train_loss = 1.446
Epoch  11 Batch    7/26   train_loss = 1.361
Epoch  11 Batch    8/26   train_loss = 1.344
Epoch  11 Batch    9/26   train_loss = 1.423
Epoch  11 Batch   10/26   train_loss = 1.371
Epoch  11 Batch   11/26   train_loss = 1.373
Epoch  11 Batch   12/26   train_loss = 1.327
Epoch  11 Batch   13/26   train_loss = 1.297
Epoch  11 Batch   14/26   train_loss = 1.270
Epoch  11 Batch   15/26   train_loss = 1.285
Epoch  11 Batch   16/26   train_loss = 1.262
Epoch  11 Batch   17/26   train_loss = 1.268
Epoch  11 Batch   18/26   train_loss = 1.276
Epoch  11 Batch   19/26   train_loss = 1.249
Epoch  11 Batch   20/26   train_loss = 1.247
Epoch  11 Batch   21/26   train_loss = 1.202
Epoch  11 Batch   22/26   train_loss = 1.196
Epoch  11 Batch   23/26   train_loss = 1.194
Epoch  11 Batch   24/26   train_loss = 1.283
Epoch  11 Batch   25/26   train_loss = 1.144
Epoch  12 Batch    0/26   train_loss = 1.171
Epoch  12 Batch    1/26   train_loss = 1.193
Epoch  12 Batch    2/26   train_loss = 1.163
Epoch  12 Batch    3/26   train_loss = 1.160
Epoch  12 Batch    4/26   train_loss = 1.125
Epoch  12 Batch    5/26   train_loss = 1.131
Epoch  12 Batch    6/26   train_loss = 1.198
Epoch  12 Batch    7/26   train_loss = 1.206
Epoch  12 Batch    8/26   train_loss = 1.131
Epoch  12 Batch    9/26   train_loss = 1.122
Epoch  12 Batch   10/26   train_loss = 1.061
Epoch  12 Batch   11/26   train_loss = 1.153
Epoch  12 Batch   12/26   train_loss = 1.126
Epoch  12 Batch   13/26   train_loss = 1.190
Epoch  12 Batch   14/26   train_loss = 1.063
Epoch  12 Batch   15/26   train_loss = 1.061
Epoch  12 Batch   16/26   train_loss = 1.015
Epoch  12 Batch   17/26   train_loss = 0.970
Epoch  12 Batch   18/26   train_loss = 1.047
Epoch  12 Batch   19/26   train_loss = 0.990
Epoch  12 Batch   20/26   train_loss = 1.054
Epoch  12 Batch   21/26   train_loss = 0.998
Epoch  12 Batch   22/26   train_loss = 1.040
Epoch  12 Batch   23/26   train_loss = 1.033
Epoch  12 Batch   24/26   train_loss = 1.011
Epoch  12 Batch   25/26   train_loss = 0.966
Epoch  13 Batch    0/26   train_loss = 0.948
Epoch  13 Batch    1/26   train_loss = 1.010
Epoch  13 Batch    2/26   train_loss = 1.000
Epoch  13 Batch    3/26   train_loss = 0.978
Epoch  13 Batch    4/26   train_loss = 0.890
Epoch  13 Batch    5/26   train_loss = 0.985
Epoch  13 Batch    6/26   train_loss = 0.949
Epoch  13 Batch    7/26   train_loss = 0.942
Epoch  13 Batch    8/26   train_loss = 0.888
Epoch  13 Batch    9/26   train_loss = 0.895
Epoch  13 Batch   10/26   train_loss = 0.873
Epoch  13 Batch   11/26   train_loss = 0.941
Epoch  13 Batch   12/26   train_loss = 0.892
Epoch  13 Batch   13/26   train_loss = 0.910
Epoch  13 Batch   14/26   train_loss = 0.881
Epoch  13 Batch   15/26   train_loss = 0.864
Epoch  13 Batch   16/26   train_loss = 0.858
Epoch  13 Batch   17/26   train_loss = 0.819
Epoch  13 Batch   18/26   train_loss = 0.874
Epoch  13 Batch   19/26   train_loss = 0.889
Epoch  13 Batch   20/26   train_loss = 0.888
Epoch  13 Batch   21/26   train_loss = 0.823
Epoch  13 Batch   22/26   train_loss = 0.773
Epoch  13 Batch   23/26   train_loss = 0.821
Epoch  13 Batch   24/26   train_loss = 0.834
Epoch  13 Batch   25/26   train_loss = 0.796
Epoch  14 Batch    0/26   train_loss = 0.816
Epoch  14 Batch    1/26   train_loss = 0.817
Epoch  14 Batch    2/26   train_loss = 0.816
Epoch  14 Batch    3/26   train_loss = 0.811
Epoch  14 Batch    4/26   train_loss = 0.773
Epoch  14 Batch    5/26   train_loss = 0.787
Epoch  14 Batch    6/26   train_loss = 0.753
Epoch  14 Batch    7/26   train_loss = 0.784
Epoch  14 Batch    8/26   train_loss = 0.770
Epoch  14 Batch    9/26   train_loss = 0.755
Epoch  14 Batch   10/26   train_loss = 0.724
Epoch  14 Batch   11/26   train_loss = 0.791
Epoch  14 Batch   12/26   train_loss = 0.776
Epoch  14 Batch   13/26   train_loss = 0.766
Epoch  14 Batch   14/26   train_loss = 0.745
Epoch  14 Batch   15/26   train_loss = 0.679
Epoch  14 Batch   16/26   train_loss = 0.729
Epoch  14 Batch   17/26   train_loss = 0.733
Epoch  14 Batch   18/26   train_loss = 0.742
Epoch  14 Batch   19/26   train_loss = 0.704
Epoch  14 Batch   20/26   train_loss = 0.722
Epoch  14 Batch   21/26   train_loss = 0.769
Epoch  14 Batch   22/26   train_loss = 0.728
Epoch  14 Batch   23/26   train_loss = 0.695
Epoch  14 Batch   24/26   train_loss = 0.704
Epoch  14 Batch   25/26   train_loss = 0.644
Epoch  15 Batch    0/26   train_loss = 0.763
Epoch  15 Batch    1/26   train_loss = 0.688
Epoch  15 Batch    2/26   train_loss = 0.634
Epoch  15 Batch    3/26   train_loss = 0.664
Epoch  15 Batch    4/26   train_loss = 0.735
Epoch  15 Batch    5/26   train_loss = 0.762
Epoch  15 Batch    6/26   train_loss = 0.699
Epoch  15 Batch    7/26   train_loss = 0.684
Epoch  15 Batch    8/26   train_loss = 0.660
Epoch  15 Batch    9/26   train_loss = 0.687
Epoch  15 Batch   10/26   train_loss = 0.745
Epoch  15 Batch   11/26   train_loss = 0.669
Epoch  15 Batch   12/26   train_loss = 0.607
Epoch  15 Batch   13/26   train_loss = 0.579
Epoch  15 Batch   14/26   train_loss = 0.671
Epoch  15 Batch   15/26   train_loss = 0.703
Epoch  15 Batch   16/26   train_loss = 0.662
Epoch  15 Batch   17/26   train_loss = 0.587
Epoch  15 Batch   18/26   train_loss = 0.596
Epoch  15 Batch   19/26   train_loss = 0.621
Epoch  15 Batch   20/26   train_loss = 0.660
Epoch  15 Batch   21/26   train_loss = 0.577
Epoch  15 Batch   22/26   train_loss = 0.565
Epoch  15 Batch   23/26   train_loss = 0.553
Epoch  15 Batch   24/26   train_loss = 0.607
Epoch  15 Batch   25/26   train_loss = 0.558
Epoch  16 Batch    0/26   train_loss = 0.578
Epoch  16 Batch    1/26   train_loss = 0.556
Epoch  16 Batch    2/26   train_loss = 0.546
Epoch  16 Batch    3/26   train_loss = 0.554
Epoch  16 Batch    4/26   train_loss = 0.574
Epoch  16 Batch    5/26   train_loss = 0.540
Epoch  16 Batch    6/26   train_loss = 0.550
Epoch  16 Batch    7/26   train_loss = 0.581
Epoch  16 Batch    8/26   train_loss = 0.598
Epoch  16 Batch    9/26   train_loss = 0.570
Epoch  16 Batch   10/26   train_loss = 0.524
Epoch  16 Batch   11/26   train_loss = 0.519
Epoch  16 Batch   12/26   train_loss = 0.563
Epoch  16 Batch   13/26   train_loss = 0.522
Epoch  16 Batch   14/26   train_loss = 0.510
Epoch  16 Batch   15/26   train_loss = 0.510
Epoch  16 Batch   16/26   train_loss = 0.467
Epoch  16 Batch   17/26   train_loss = 0.476
Epoch  16 Batch   18/26   train_loss = 0.491
Epoch  16 Batch   19/26   train_loss = 0.502
Epoch  16 Batch   20/26   train_loss = 0.500
Epoch  16 Batch   21/26   train_loss = 0.478
Epoch  16 Batch   22/26   train_loss = 0.454
Epoch  16 Batch   23/26   train_loss = 0.447
Epoch  16 Batch   24/26   train_loss = 0.516
Epoch  16 Batch   25/26   train_loss = 0.438
Epoch  17 Batch    0/26   train_loss = 0.478
Epoch  17 Batch    1/26   train_loss = 0.463
Epoch  17 Batch    2/26   train_loss = 0.450
Epoch  17 Batch    3/26   train_loss = 0.457
Epoch  17 Batch    4/26   train_loss = 0.435
Epoch  17 Batch    5/26   train_loss = 0.469
Epoch  17 Batch    6/26   train_loss = 0.449
Epoch  17 Batch    7/26   train_loss = 0.458
Epoch  17 Batch    8/26   train_loss = 0.401
Epoch  17 Batch    9/26   train_loss = 0.438
Epoch  17 Batch   10/26   train_loss = 0.450
Epoch  17 Batch   11/26   train_loss = 0.427
Epoch  17 Batch   12/26   train_loss = 0.425
Epoch  17 Batch   13/26   train_loss = 0.394
Epoch  17 Batch   14/26   train_loss = 0.421
Epoch  17 Batch   15/26   train_loss = 0.459
Epoch  17 Batch   16/26   train_loss = 0.412
Epoch  17 Batch   17/26   train_loss = 0.417
Epoch  17 Batch   18/26   train_loss = 0.401
Epoch  17 Batch   19/26   train_loss = 0.408
Epoch  17 Batch   20/26   train_loss = 0.402
Epoch  17 Batch   21/26   train_loss = 0.390
Epoch  17 Batch   22/26   train_loss = 0.367
Epoch  17 Batch   23/26   train_loss = 0.388
Epoch  17 Batch   24/26   train_loss = 0.437
Epoch  17 Batch   25/26   train_loss = 0.350
Epoch  18 Batch    0/26   train_loss = 0.401
Epoch  18 Batch    1/26   train_loss = 0.418
Epoch  18 Batch    2/26   train_loss = 0.381
Epoch  18 Batch    3/26   train_loss = 0.384
Epoch  18 Batch    4/26   train_loss = 0.381
Epoch  18 Batch    5/26   train_loss = 0.391
Epoch  18 Batch    6/26   train_loss = 0.382
Epoch  18 Batch    7/26   train_loss = 0.385
Epoch  18 Batch    8/26   train_loss = 0.332
Epoch  18 Batch    9/26   train_loss = 0.385
Epoch  18 Batch   10/26   train_loss = 0.370
Epoch  18 Batch   11/26   train_loss = 0.363
Epoch  18 Batch   12/26   train_loss = 0.378
Epoch  18 Batch   13/26   train_loss = 0.346
Epoch  18 Batch   14/26   train_loss = 0.343
Epoch  18 Batch   15/26   train_loss = 0.355
Epoch  18 Batch   16/26   train_loss = 0.359
Epoch  18 Batch   17/26   train_loss = 0.357
Epoch  18 Batch   18/26   train_loss = 0.346
Epoch  18 Batch   19/26   train_loss = 0.372
Epoch  18 Batch   20/26   train_loss = 0.357
Epoch  18 Batch   21/26   train_loss = 0.328
Epoch  18 Batch   22/26   train_loss = 0.317
Epoch  18 Batch   23/26   train_loss = 0.329
Epoch  18 Batch   24/26   train_loss = 0.378
Epoch  18 Batch   25/26   train_loss = 0.333
Epoch  19 Batch    0/26   train_loss = 0.345
Epoch  19 Batch    1/26   train_loss = 0.330
Epoch  19 Batch    2/26   train_loss = 0.335
Epoch  19 Batch    3/26   train_loss = 0.353
Epoch  19 Batch    4/26   train_loss = 0.342
Epoch  19 Batch    5/26   train_loss = 0.310
Epoch  19 Batch    6/26   train_loss = 0.333
Epoch  19 Batch    7/26   train_loss = 0.329
Epoch  19 Batch    8/26   train_loss = 0.296
Epoch  19 Batch    9/26   train_loss = 0.333
Epoch  19 Batch   10/26   train_loss = 0.333
Epoch  19 Batch   11/26   train_loss = 0.324
Epoch  19 Batch   12/26   train_loss = 0.335
Epoch  19 Batch   13/26   train_loss = 0.304
Epoch  19 Batch   14/26   train_loss = 0.310
Epoch  19 Batch   15/26   train_loss = 0.314
Epoch  19 Batch   16/26   train_loss = 0.322
Epoch  19 Batch   17/26   train_loss = 0.318
Epoch  19 Batch   18/26   train_loss = 0.309
Epoch  19 Batch   19/26   train_loss = 0.304
Epoch  19 Batch   20/26   train_loss = 0.340
Epoch  19 Batch   21/26   train_loss = 0.301
Epoch  19 Batch   22/26   train_loss = 0.290
Epoch  19 Batch   23/26   train_loss = 0.286
Epoch  19 Batch   24/26   train_loss = 0.315
Epoch  19 Batch   25/26   train_loss = 0.282
Epoch  20 Batch    0/26   train_loss = 0.303
Epoch  20 Batch    1/26   train_loss = 0.303
Epoch  20 Batch    2/26   train_loss = 0.288
Epoch  20 Batch    3/26   train_loss = 0.300
Epoch  20 Batch    4/26   train_loss = 0.306
Epoch  20 Batch    5/26   train_loss = 0.297
Epoch  20 Batch    6/26   train_loss = 0.311
Epoch  20 Batch    7/26   train_loss = 0.303
Epoch  20 Batch    8/26   train_loss = 0.276
Epoch  20 Batch    9/26   train_loss = 0.295
Epoch  20 Batch   10/26   train_loss = 0.283
Epoch  20 Batch   11/26   train_loss = 0.283
Epoch  20 Batch   12/26   train_loss = 0.318
Epoch  20 Batch   13/26   train_loss = 0.285
Epoch  20 Batch   14/26   train_loss = 0.296
Epoch  20 Batch   15/26   train_loss = 0.288
Epoch  20 Batch   16/26   train_loss = 0.283
Epoch  20 Batch   17/26   train_loss = 0.289
Epoch  20 Batch   18/26   train_loss = 0.284
Epoch  20 Batch   19/26   train_loss = 0.301
Epoch  20 Batch   20/26   train_loss = 0.295
Epoch  20 Batch   21/26   train_loss = 0.302
Epoch  20 Batch   22/26   train_loss = 0.270
Epoch  20 Batch   23/26   train_loss = 0.259
Epoch  20 Batch   24/26   train_loss = 0.293
Epoch  20 Batch   25/26   train_loss = 0.267
Epoch  21 Batch    0/26   train_loss = 0.272
Epoch  21 Batch    1/26   train_loss = 0.274
Epoch  21 Batch    2/26   train_loss = 0.287
Epoch  21 Batch    3/26   train_loss = 0.296
Epoch  21 Batch    4/26   train_loss = 0.285
Epoch  21 Batch    5/26   train_loss = 0.258
Epoch  21 Batch    6/26   train_loss = 0.279
Epoch  21 Batch    7/26   train_loss = 0.307
Epoch  21 Batch    8/26   train_loss = 0.288
Epoch  21 Batch    9/26   train_loss = 0.293
Epoch  21 Batch   10/26   train_loss = 0.274
Epoch  21 Batch   11/26   train_loss = 0.265
Epoch  21 Batch   12/26   train_loss = 0.290
Epoch  21 Batch   13/26   train_loss = 0.244
Epoch  21 Batch   14/26   train_loss = 0.285
Epoch  21 Batch   15/26   train_loss = 0.312
Epoch  21 Batch   16/26   train_loss = 0.291
Epoch  21 Batch   17/26   train_loss = 0.262
Epoch  21 Batch   18/26   train_loss = 0.257
Epoch  21 Batch   19/26   train_loss = 0.270
Epoch  21 Batch   20/26   train_loss = 0.273
Epoch  21 Batch   21/26   train_loss = 0.275
Epoch  21 Batch   22/26   train_loss = 0.314
Epoch  21 Batch   23/26   train_loss = 0.241
Epoch  21 Batch   24/26   train_loss = 0.271
Epoch  21 Batch   25/26   train_loss = 0.231
Epoch  22 Batch    0/26   train_loss = 0.246
Epoch  22 Batch    1/26   train_loss = 0.263
Epoch  22 Batch    2/26   train_loss = 0.286
Epoch  22 Batch    3/26   train_loss = 0.296
Epoch  22 Batch    4/26   train_loss = 0.265
Epoch  22 Batch    5/26   train_loss = 0.270
Epoch  22 Batch    6/26   train_loss = 0.261
Epoch  22 Batch    7/26   train_loss = 0.270
Epoch  22 Batch    8/26   train_loss = 0.249
Epoch  22 Batch    9/26   train_loss = 0.269
Epoch  22 Batch   10/26   train_loss = 0.268
Epoch  22 Batch   11/26   train_loss = 0.268
Epoch  22 Batch   12/26   train_loss = 0.261
Epoch  22 Batch   13/26   train_loss = 0.240
Epoch  22 Batch   14/26   train_loss = 0.252
Epoch  22 Batch   15/26   train_loss = 0.251
Epoch  22 Batch   16/26   train_loss = 0.262
Epoch  22 Batch   17/26   train_loss = 0.274
Epoch  22 Batch   18/26   train_loss = 0.272
Epoch  22 Batch   19/26   train_loss = 0.277
Epoch  22 Batch   20/26   train_loss = 0.258
Epoch  22 Batch   21/26   train_loss = 0.271
Epoch  22 Batch   22/26   train_loss = 0.231
Epoch  22 Batch   23/26   train_loss = 0.233
Epoch  22 Batch   24/26   train_loss = 0.276
Epoch  22 Batch   25/26   train_loss = 0.222
Epoch  23 Batch    0/26   train_loss = 0.252
Epoch  23 Batch    1/26   train_loss = 0.230
Epoch  23 Batch    2/26   train_loss = 0.267
Epoch  23 Batch    3/26   train_loss = 0.243
Epoch  23 Batch    4/26   train_loss = 0.251
Epoch  23 Batch    5/26   train_loss = 0.239
Epoch  23 Batch    6/26   train_loss = 0.235
Epoch  23 Batch    7/26   train_loss = 0.257
Epoch  23 Batch    8/26   train_loss = 0.237
Epoch  23 Batch    9/26   train_loss = 0.250
Epoch  23 Batch   10/26   train_loss = 0.242
Epoch  23 Batch   11/26   train_loss = 0.238
Epoch  23 Batch   12/26   train_loss = 0.246
Epoch  23 Batch   13/26   train_loss = 0.219
Epoch  23 Batch   14/26   train_loss = 0.254
Epoch  23 Batch   15/26   train_loss = 0.251
Epoch  23 Batch   16/26   train_loss = 0.250
Epoch  23 Batch   17/26   train_loss = 0.240
Epoch  23 Batch   18/26   train_loss = 0.227
Epoch  23 Batch   19/26   train_loss = 0.240
Epoch  23 Batch   20/26   train_loss = 0.230
Epoch  23 Batch   21/26   train_loss = 0.244
Epoch  23 Batch   22/26   train_loss = 0.223
Epoch  23 Batch   23/26   train_loss = 0.210
Epoch  23 Batch   24/26   train_loss = 0.251
Epoch  23 Batch   25/26   train_loss = 0.216
Epoch  24 Batch    0/26   train_loss = 0.239
Epoch  24 Batch    1/26   train_loss = 0.214
Epoch  24 Batch    2/26   train_loss = 0.249
Epoch  24 Batch    3/26   train_loss = 0.241
Epoch  24 Batch    4/26   train_loss = 0.241
Epoch  24 Batch    5/26   train_loss = 0.209
Epoch  24 Batch    6/26   train_loss = 0.229
Epoch  24 Batch    7/26   train_loss = 0.233
Epoch  24 Batch    8/26   train_loss = 0.208
Epoch  24 Batch    9/26   train_loss = 0.239
Epoch  24 Batch   10/26   train_loss = 0.215
Epoch  24 Batch   11/26   train_loss = 0.220
Epoch  24 Batch   12/26   train_loss = 0.249
Epoch  24 Batch   13/26   train_loss = 0.191
Epoch  24 Batch   14/26   train_loss = 0.222
Epoch  24 Batch   15/26   train_loss = 0.232
Epoch  24 Batch   16/26   train_loss = 0.226
Epoch  24 Batch   17/26   train_loss = 0.227
Epoch  24 Batch   18/26   train_loss = 0.225
Epoch  24 Batch   19/26   train_loss = 0.230
Epoch  24 Batch   20/26   train_loss = 0.224
Epoch  24 Batch   21/26   train_loss = 0.233
Epoch  24 Batch   22/26   train_loss = 0.212
Epoch  24 Batch   23/26   train_loss = 0.202
Epoch  24 Batch   24/26   train_loss = 0.213
Epoch  24 Batch   25/26   train_loss = 0.201
Epoch  25 Batch    0/26   train_loss = 0.212
Epoch  25 Batch    1/26   train_loss = 0.214
Epoch  25 Batch    2/26   train_loss = 0.223
Epoch  25 Batch    3/26   train_loss = 0.247
Epoch  25 Batch    4/26   train_loss = 0.230
Epoch  25 Batch    5/26   train_loss = 0.210
Epoch  25 Batch    6/26   train_loss = 0.208
Epoch  25 Batch    7/26   train_loss = 0.215
Epoch  25 Batch    8/26   train_loss = 0.192
Epoch  25 Batch    9/26   train_loss = 0.216
Epoch  25 Batch   10/26   train_loss = 0.212
Epoch  25 Batch   11/26   train_loss = 0.205
Epoch  25 Batch   12/26   train_loss = 0.225
Epoch  25 Batch   13/26   train_loss = 0.175
Epoch  25 Batch   14/26   train_loss = 0.208
Epoch  25 Batch   15/26   train_loss = 0.241
Epoch  25 Batch   16/26   train_loss = 0.227
Epoch  25 Batch   17/26   train_loss = 0.210
Epoch  25 Batch   18/26   train_loss = 0.213
Epoch  25 Batch   19/26   train_loss = 0.221
Epoch  25 Batch   20/26   train_loss = 0.213
Epoch  25 Batch   21/26   train_loss = 0.201
Epoch  25 Batch   22/26   train_loss = 0.194
Epoch  25 Batch   23/26   train_loss = 0.192
Epoch  25 Batch   24/26   train_loss = 0.211
Epoch  25 Batch   25/26   train_loss = 0.203
Epoch  26 Batch    0/26   train_loss = 0.211
Epoch  26 Batch    1/26   train_loss = 0.205
Epoch  26 Batch    2/26   train_loss = 0.213
Epoch  26 Batch    3/26   train_loss = 0.249
Epoch  26 Batch    4/26   train_loss = 0.220
Epoch  26 Batch    5/26   train_loss = 0.198
Epoch  26 Batch    6/26   train_loss = 0.202
Epoch  26 Batch    7/26   train_loss = 0.209
Epoch  26 Batch    8/26   train_loss = 0.188
Epoch  26 Batch    9/26   train_loss = 0.204
Epoch  26 Batch   10/26   train_loss = 0.190
Epoch  26 Batch   11/26   train_loss = 0.197
Epoch  26 Batch   12/26   train_loss = 0.205
Epoch  26 Batch   13/26   train_loss = 0.171
Epoch  26 Batch   14/26   train_loss = 0.206
Epoch  26 Batch   15/26   train_loss = 0.233
Epoch  26 Batch   16/26   train_loss = 0.219
Epoch  26 Batch   17/26   train_loss = 0.219
Epoch  26 Batch   18/26   train_loss = 0.218
Epoch  26 Batch   19/26   train_loss = 0.210
Epoch  26 Batch   20/26   train_loss = 0.211
Epoch  26 Batch   21/26   train_loss = 0.212
Epoch  26 Batch   22/26   train_loss = 0.188
Epoch  26 Batch   23/26   train_loss = 0.188
Epoch  26 Batch   24/26   train_loss = 0.192
Epoch  26 Batch   25/26   train_loss = 0.193
Epoch  27 Batch    0/26   train_loss = 0.197
Epoch  27 Batch    1/26   train_loss = 0.193
Epoch  27 Batch    2/26   train_loss = 0.208
Epoch  27 Batch    3/26   train_loss = 0.270
Epoch  27 Batch    4/26   train_loss = 0.215
Epoch  27 Batch    5/26   train_loss = 0.193
Epoch  27 Batch    6/26   train_loss = 0.192
Epoch  27 Batch    7/26   train_loss = 0.202
Epoch  27 Batch    8/26   train_loss = 0.187
Epoch  27 Batch    9/26   train_loss = 0.201
Epoch  27 Batch   10/26   train_loss = 0.189
Epoch  27 Batch   11/26   train_loss = 0.206
Epoch  27 Batch   12/26   train_loss = 0.197
Epoch  27 Batch   13/26   train_loss = 0.185
Epoch  27 Batch   14/26   train_loss = 0.201
Epoch  27 Batch   15/26   train_loss = 0.237
Epoch  27 Batch   16/26   train_loss = 0.253
Epoch  27 Batch   17/26   train_loss = 0.201
Epoch  27 Batch   18/26   train_loss = 0.212
Epoch  27 Batch   19/26   train_loss = 0.200
Epoch  27 Batch   20/26   train_loss = 0.208
Epoch  27 Batch   21/26   train_loss = 0.191
Epoch  27 Batch   22/26   train_loss = 0.186
Epoch  27 Batch   23/26   train_loss = 0.182
Epoch  27 Batch   24/26   train_loss = 0.203
Epoch  27 Batch   25/26   train_loss = 0.191
Epoch  28 Batch    0/26   train_loss = 0.195
Epoch  28 Batch    1/26   train_loss = 0.181
Epoch  28 Batch    2/26   train_loss = 0.203
Epoch  28 Batch    3/26   train_loss = 0.220
Epoch  28 Batch    4/26   train_loss = 0.212
Epoch  28 Batch    5/26   train_loss = 0.182
Epoch  28 Batch    6/26   train_loss = 0.192
Epoch  28 Batch    7/26   train_loss = 0.203
Epoch  28 Batch    8/26   train_loss = 0.185
Epoch  28 Batch    9/26   train_loss = 0.194
Epoch  28 Batch   10/26   train_loss = 0.180
Epoch  28 Batch   11/26   train_loss = 0.207
Epoch  28 Batch   12/26   train_loss = 0.199
Epoch  28 Batch   13/26   train_loss = 0.174
Epoch  28 Batch   14/26   train_loss = 0.205
Epoch  28 Batch   15/26   train_loss = 0.212
Epoch  28 Batch   16/26   train_loss = 0.209
Epoch  28 Batch   17/26   train_loss = 0.202
Epoch  28 Batch   18/26   train_loss = 0.208
Epoch  28 Batch   19/26   train_loss = 0.189
Epoch  28 Batch   20/26   train_loss = 0.219
Epoch  28 Batch   21/26   train_loss = 0.204
Epoch  28 Batch   22/26   train_loss = 0.186
Epoch  28 Batch   23/26   train_loss = 0.183
Epoch  28 Batch   24/26   train_loss = 0.183
Epoch  28 Batch   25/26   train_loss = 0.192
Epoch  29 Batch    0/26   train_loss = 0.194
Epoch  29 Batch    1/26   train_loss = 0.184
Epoch  29 Batch    2/26   train_loss = 0.204
Epoch  29 Batch    3/26   train_loss = 0.201
Epoch  29 Batch    4/26   train_loss = 0.212
Epoch  29 Batch    5/26   train_loss = 0.183
Epoch  29 Batch    6/26   train_loss = 0.187
Epoch  29 Batch    7/26   train_loss = 0.218
Epoch  29 Batch    8/26   train_loss = 0.182
Epoch  29 Batch    9/26   train_loss = 0.194
Epoch  29 Batch   10/26   train_loss = 0.188
Epoch  29 Batch   11/26   train_loss = 0.200
Epoch  29 Batch   12/26   train_loss = 0.192
Epoch  29 Batch   13/26   train_loss = 0.163
Epoch  29 Batch   14/26   train_loss = 0.203
Epoch  29 Batch   15/26   train_loss = 0.229
Epoch  29 Batch   16/26   train_loss = 0.212
Epoch  29 Batch   17/26   train_loss = 0.193
Epoch  29 Batch   18/26   train_loss = 0.202
Epoch  29 Batch   19/26   train_loss = 0.192
Epoch  29 Batch   20/26   train_loss = 0.203
Epoch  29 Batch   21/26   train_loss = 0.184
Epoch  29 Batch   22/26   train_loss = 0.186
Epoch  29 Batch   23/26   train_loss = 0.178
Epoch  29 Batch   24/26   train_loss = 0.195
Epoch  29 Batch   25/26   train_loss = 0.199
Epoch  30 Batch    0/26   train_loss = 0.199
Epoch  30 Batch    1/26   train_loss = 0.178
Epoch  30 Batch    2/26   train_loss = 0.197
Epoch  30 Batch    3/26   train_loss = 0.197
Epoch  30 Batch    4/26   train_loss = 0.211
Epoch  30 Batch    5/26   train_loss = 0.180
Epoch  30 Batch    6/26   train_loss = 0.185
Epoch  30 Batch    7/26   train_loss = 0.221
Epoch  30 Batch    8/26   train_loss = 0.171
Epoch  30 Batch    9/26   train_loss = 0.189
Epoch  30 Batch   10/26   train_loss = 0.192
Epoch  30 Batch   11/26   train_loss = 0.189
Epoch  30 Batch   12/26   train_loss = 0.198
Epoch  30 Batch   13/26   train_loss = 0.158
Epoch  30 Batch   14/26   train_loss = 0.200
Epoch  30 Batch   15/26   train_loss = 0.205
Epoch  30 Batch   16/26   train_loss = 0.203
Epoch  30 Batch   17/26   train_loss = 0.218
Epoch  30 Batch   18/26   train_loss = 0.191
Epoch  30 Batch   19/26   train_loss = 0.187
Epoch  30 Batch   20/26   train_loss = 0.223
Epoch  30 Batch   21/26   train_loss = 0.201
Epoch  30 Batch   22/26   train_loss = 0.183
Epoch  30 Batch   23/26   train_loss = 0.181
Epoch  30 Batch   24/26   train_loss = 0.179
Epoch  30 Batch   25/26   train_loss = 0.180
Epoch  31 Batch    0/26   train_loss = 0.182
Epoch  31 Batch    1/26   train_loss = 0.195
Epoch  31 Batch    2/26   train_loss = 0.201
Epoch  31 Batch    3/26   train_loss = 0.193
Epoch  31 Batch    4/26   train_loss = 0.211
Epoch  31 Batch    5/26   train_loss = 0.181
Epoch  31 Batch    6/26   train_loss = 0.185
Epoch  31 Batch    7/26   train_loss = 0.196
Epoch  31 Batch    8/26   train_loss = 0.171
Epoch  31 Batch    9/26   train_loss = 0.189
Epoch  31 Batch   10/26   train_loss = 0.181
Epoch  31 Batch   11/26   train_loss = 0.192
Epoch  31 Batch   12/26   train_loss = 0.191
Epoch  31 Batch   13/26   train_loss = 0.154
Epoch  31 Batch   14/26   train_loss = 0.193
Epoch  31 Batch   15/26   train_loss = 0.209
Epoch  31 Batch   16/26   train_loss = 0.198
Epoch  31 Batch   17/26   train_loss = 0.195
Epoch  31 Batch   18/26   train_loss = 0.190
Epoch  31 Batch   19/26   train_loss = 0.189
Epoch  31 Batch   20/26   train_loss = 0.205
Epoch  31 Batch   21/26   train_loss = 0.182
Epoch  31 Batch   22/26   train_loss = 0.181
Epoch  31 Batch   23/26   train_loss = 0.173
Epoch  31 Batch   24/26   train_loss = 0.187
Epoch  31 Batch   25/26   train_loss = 0.172
Epoch  32 Batch    0/26   train_loss = 0.186
Epoch  32 Batch    1/26   train_loss = 0.189
Epoch  32 Batch    2/26   train_loss = 0.195
Epoch  32 Batch    3/26   train_loss = 0.189
Epoch  32 Batch    4/26   train_loss = 0.205
Epoch  32 Batch    5/26   train_loss = 0.176
Epoch  32 Batch    6/26   train_loss = 0.181
Epoch  32 Batch    7/26   train_loss = 0.195
Epoch  32 Batch    8/26   train_loss = 0.177
Epoch  32 Batch    9/26   train_loss = 0.188
Epoch  32 Batch   10/26   train_loss = 0.172
Epoch  32 Batch   11/26   train_loss = 0.187
Epoch  32 Batch   12/26   train_loss = 0.191
Epoch  32 Batch   13/26   train_loss = 0.152
Epoch  32 Batch   14/26   train_loss = 0.199
Epoch  32 Batch   15/26   train_loss = 0.192
Epoch  32 Batch   16/26   train_loss = 0.198
Epoch  32 Batch   17/26   train_loss = 0.193
Epoch  32 Batch   18/26   train_loss = 0.187
Epoch  32 Batch   19/26   train_loss = 0.185
Epoch  32 Batch   20/26   train_loss = 0.194
Epoch  32 Batch   21/26   train_loss = 0.205
Epoch  32 Batch   22/26   train_loss = 0.177
Epoch  32 Batch   23/26   train_loss = 0.182
Epoch  32 Batch   24/26   train_loss = 0.175
Epoch  32 Batch   25/26   train_loss = 0.171
Epoch  33 Batch    0/26   train_loss = 0.181
Epoch  33 Batch    1/26   train_loss = 0.187
Epoch  33 Batch    2/26   train_loss = 0.196
Epoch  33 Batch    3/26   train_loss = 0.187
Epoch  33 Batch    4/26   train_loss = 0.201
Epoch  33 Batch    5/26   train_loss = 0.177
Epoch  33 Batch    6/26   train_loss = 0.181
Epoch  33 Batch    7/26   train_loss = 0.187
Epoch  33 Batch    8/26   train_loss = 0.173
Epoch  33 Batch    9/26   train_loss = 0.196
Epoch  33 Batch   10/26   train_loss = 0.176
Epoch  33 Batch   11/26   train_loss = 0.185
Epoch  33 Batch   12/26   train_loss = 0.187
Epoch  33 Batch   13/26   train_loss = 0.149
Epoch  33 Batch   14/26   train_loss = 0.199
Epoch  33 Batch   15/26   train_loss = 0.201
Epoch  33 Batch   16/26   train_loss = 0.196
Epoch  33 Batch   17/26   train_loss = 0.183
Epoch  33 Batch   18/26   train_loss = 0.189
Epoch  33 Batch   19/26   train_loss = 0.186
Epoch  33 Batch   20/26   train_loss = 0.186
Epoch  33 Batch   21/26   train_loss = 0.186
Epoch  33 Batch   22/26   train_loss = 0.179
Epoch  33 Batch   23/26   train_loss = 0.191
Epoch  33 Batch   24/26   train_loss = 0.184
Epoch  33 Batch   25/26   train_loss = 0.175
Epoch  34 Batch    0/26   train_loss = 0.188
Epoch  34 Batch    1/26   train_loss = 0.174
Epoch  34 Batch    2/26   train_loss = 0.191
Epoch  34 Batch    3/26   train_loss = 0.187
Epoch  34 Batch    4/26   train_loss = 0.201
Epoch  34 Batch    5/26   train_loss = 0.173
Epoch  34 Batch    6/26   train_loss = 0.178
Epoch  34 Batch    7/26   train_loss = 0.193
Epoch  34 Batch    8/26   train_loss = 0.168
Epoch  34 Batch    9/26   train_loss = 0.187
Epoch  34 Batch   10/26   train_loss = 0.170
Epoch  34 Batch   11/26   train_loss = 0.184
Epoch  34 Batch   12/26   train_loss = 0.189
Epoch  34 Batch   13/26   train_loss = 0.149
Epoch  34 Batch   14/26   train_loss = 0.194
Epoch  34 Batch   15/26   train_loss = 0.189
Epoch  34 Batch   16/26   train_loss = 0.196
Epoch  34 Batch   17/26   train_loss = 0.187
Epoch  34 Batch   18/26   train_loss = 0.184
Epoch  34 Batch   19/26   train_loss = 0.183
Epoch  34 Batch   20/26   train_loss = 0.184
Epoch  34 Batch   21/26   train_loss = 0.195
Epoch  34 Batch   22/26   train_loss = 0.178
Epoch  34 Batch   23/26   train_loss = 0.183
Epoch  34 Batch   24/26   train_loss = 0.172
Epoch  34 Batch   25/26   train_loss = 0.169
Epoch  35 Batch    0/26   train_loss = 0.180
Epoch  35 Batch    1/26   train_loss = 0.179
Epoch  35 Batch    2/26   train_loss = 0.193
Epoch  35 Batch    3/26   train_loss = 0.183
Epoch  35 Batch    4/26   train_loss = 0.200
Epoch  35 Batch    5/26   train_loss = 0.170
Epoch  35 Batch    6/26   train_loss = 0.181
Epoch  35 Batch    7/26   train_loss = 0.187
Epoch  35 Batch    8/26   train_loss = 0.166
Epoch  35 Batch    9/26   train_loss = 0.185
Epoch  35 Batch   10/26   train_loss = 0.173
Epoch  35 Batch   11/26   train_loss = 0.178
Epoch  35 Batch   12/26   train_loss = 0.185
Epoch  35 Batch   13/26   train_loss = 0.148
Epoch  35 Batch   14/26   train_loss = 0.193
Epoch  35 Batch   15/26   train_loss = 0.198
Epoch  35 Batch   16/26   train_loss = 0.194
Epoch  35 Batch   17/26   train_loss = 0.182
Epoch  35 Batch   18/26   train_loss = 0.185
Epoch  35 Batch   19/26   train_loss = 0.189
Epoch  35 Batch   20/26   train_loss = 0.183
Epoch  35 Batch   21/26   train_loss = 0.190
Epoch  35 Batch   22/26   train_loss = 0.176
Epoch  35 Batch   23/26   train_loss = 0.171
Epoch  35 Batch   24/26   train_loss = 0.180
Epoch  35 Batch   25/26   train_loss = 0.172
Epoch  36 Batch    0/26   train_loss = 0.185
Epoch  36 Batch    1/26   train_loss = 0.169
Epoch  36 Batch    2/26   train_loss = 0.188
Epoch  36 Batch    3/26   train_loss = 0.183
Epoch  36 Batch    4/26   train_loss = 0.200
Epoch  36 Batch    5/26   train_loss = 0.173
Epoch  36 Batch    6/26   train_loss = 0.176
Epoch  36 Batch    7/26   train_loss = 0.190
Epoch  36 Batch    8/26   train_loss = 0.166
Epoch  36 Batch    9/26   train_loss = 0.181
Epoch  36 Batch   10/26   train_loss = 0.170
Epoch  36 Batch   11/26   train_loss = 0.182
Epoch  36 Batch   12/26   train_loss = 0.185
Epoch  36 Batch   13/26   train_loss = 0.149
Epoch  36 Batch   14/26   train_loss = 0.192
Epoch  36 Batch   15/26   train_loss = 0.187
Epoch  36 Batch   16/26   train_loss = 0.193
Epoch  36 Batch   17/26   train_loss = 0.183
Epoch  36 Batch   18/26   train_loss = 0.181
Epoch  36 Batch   19/26   train_loss = 0.178
Epoch  36 Batch   20/26   train_loss = 0.179
Epoch  36 Batch   21/26   train_loss = 0.199
Epoch  36 Batch   22/26   train_loss = 0.175
Epoch  36 Batch   23/26   train_loss = 0.175
Epoch  36 Batch   24/26   train_loss = 0.170
Epoch  36 Batch   25/26   train_loss = 0.168
Epoch  37 Batch    0/26   train_loss = 0.179
Epoch  37 Batch    1/26   train_loss = 0.177
Epoch  37 Batch    2/26   train_loss = 0.191
Epoch  37 Batch    3/26   train_loss = 0.180
Epoch  37 Batch    4/26   train_loss = 0.196
Epoch  37 Batch    5/26   train_loss = 0.170
Epoch  37 Batch    6/26   train_loss = 0.180
Epoch  37 Batch    7/26   train_loss = 0.185
Epoch  37 Batch    8/26   train_loss = 0.166
Epoch  37 Batch    9/26   train_loss = 0.182
Epoch  37 Batch   10/26   train_loss = 0.171
Epoch  37 Batch   11/26   train_loss = 0.176
Epoch  37 Batch   12/26   train_loss = 0.182
Epoch  37 Batch   13/26   train_loss = 0.147
Epoch  37 Batch   14/26   train_loss = 0.189
Epoch  37 Batch   15/26   train_loss = 0.196
Epoch  37 Batch   16/26   train_loss = 0.192
Epoch  37 Batch   17/26   train_loss = 0.181
Epoch  37 Batch   18/26   train_loss = 0.182
Epoch  37 Batch   19/26   train_loss = 0.186
Epoch  37 Batch   20/26   train_loss = 0.181
Epoch  37 Batch   21/26   train_loss = 0.172
Epoch  37 Batch   22/26   train_loss = 0.176
Epoch  37 Batch   23/26   train_loss = 0.170
Epoch  37 Batch   24/26   train_loss = 0.178
Epoch  37 Batch   25/26   train_loss = 0.172
Epoch  38 Batch    0/26   train_loss = 0.184
Epoch  38 Batch    1/26   train_loss = 0.169
Epoch  38 Batch    2/26   train_loss = 0.188
Epoch  38 Batch    3/26   train_loss = 0.181
Epoch  38 Batch    4/26   train_loss = 0.202
Epoch  38 Batch    5/26   train_loss = 0.167
Epoch  38 Batch    6/26   train_loss = 0.175
Epoch  38 Batch    7/26   train_loss = 0.189
Epoch  38 Batch    8/26   train_loss = 0.168
Epoch  38 Batch    9/26   train_loss = 0.179
Epoch  38 Batch   10/26   train_loss = 0.169
Epoch  38 Batch   11/26   train_loss = 0.181
Epoch  38 Batch   12/26   train_loss = 0.184
Epoch  38 Batch   13/26   train_loss = 0.147
Epoch  38 Batch   14/26   train_loss = 0.184
Epoch  38 Batch   15/26   train_loss = 0.185
Epoch  38 Batch   16/26   train_loss = 0.192
Epoch  38 Batch   17/26   train_loss = 0.181
Epoch  38 Batch   18/26   train_loss = 0.179
Epoch  38 Batch   19/26   train_loss = 0.177
Epoch  38 Batch   20/26   train_loss = 0.178
Epoch  38 Batch   21/26   train_loss = 0.185
Epoch  38 Batch   22/26   train_loss = 0.173
Epoch  38 Batch   23/26   train_loss = 0.173
Epoch  38 Batch   24/26   train_loss = 0.170
Epoch  38 Batch   25/26   train_loss = 0.166
Epoch  39 Batch    0/26   train_loss = 0.179
Epoch  39 Batch    1/26   train_loss = 0.179
Epoch  39 Batch    2/26   train_loss = 0.188
Epoch  39 Batch    3/26   train_loss = 0.178
Epoch  39 Batch    4/26   train_loss = 0.196
Epoch  39 Batch    5/26   train_loss = 0.166
Epoch  39 Batch    6/26   train_loss = 0.178
Epoch  39 Batch    7/26   train_loss = 0.184
Epoch  39 Batch    8/26   train_loss = 0.175
Epoch  39 Batch    9/26   train_loss = 0.181
Epoch  39 Batch   10/26   train_loss = 0.170
Epoch  39 Batch   11/26   train_loss = 0.174
Epoch  39 Batch   12/26   train_loss = 0.180
Epoch  39 Batch   13/26   train_loss = 0.146
Epoch  39 Batch   14/26   train_loss = 0.188
Epoch  39 Batch   15/26   train_loss = 0.194
Epoch  39 Batch   16/26   train_loss = 0.190
Epoch  39 Batch   17/26   train_loss = 0.180
Epoch  39 Batch   18/26   train_loss = 0.179
Epoch  39 Batch   19/26   train_loss = 0.185
Epoch  39 Batch   20/26   train_loss = 0.178
Epoch  39 Batch   21/26   train_loss = 0.170
Epoch  39 Batch   22/26   train_loss = 0.185
Epoch  39 Batch   23/26   train_loss = 0.169
Epoch  39 Batch   24/26   train_loss = 0.176
Epoch  39 Batch   25/26   train_loss = 0.169
Epoch  40 Batch    0/26   train_loss = 0.183
Epoch  40 Batch    1/26   train_loss = 0.169
Epoch  40 Batch    2/26   train_loss = 0.188
Epoch  40 Batch    3/26   train_loss = 0.180
Epoch  40 Batch    4/26   train_loss = 0.199
Epoch  40 Batch    5/26   train_loss = 0.172
Epoch  40 Batch    6/26   train_loss = 0.176
Epoch  40 Batch    7/26   train_loss = 0.188
Epoch  40 Batch    8/26   train_loss = 0.176
Epoch  40 Batch    9/26   train_loss = 0.178
Epoch  40 Batch   10/26   train_loss = 0.169
Epoch  40 Batch   11/26   train_loss = 0.179
Epoch  40 Batch   12/26   train_loss = 0.185
Epoch  40 Batch   13/26   train_loss = 0.147
Epoch  40 Batch   14/26   train_loss = 0.184
Epoch  40 Batch   15/26   train_loss = 0.184
Epoch  40 Batch   16/26   train_loss = 0.192
Epoch  40 Batch   17/26   train_loss = 0.181
Epoch  40 Batch   18/26   train_loss = 0.181
Epoch  40 Batch   19/26   train_loss = 0.176
Epoch  40 Batch   20/26   train_loss = 0.179
Epoch  40 Batch   21/26   train_loss = 0.183
Epoch  40 Batch   22/26   train_loss = 0.188
Epoch  40 Batch   23/26   train_loss = 0.173
Epoch  40 Batch   24/26   train_loss = 0.169
Epoch  40 Batch   25/26   train_loss = 0.167
Epoch  41 Batch    0/26   train_loss = 0.177
Epoch  41 Batch    1/26   train_loss = 0.175
Epoch  41 Batch    2/26   train_loss = 0.186
Epoch  41 Batch    3/26   train_loss = 0.178
Epoch  41 Batch    4/26   train_loss = 0.198
Epoch  41 Batch    5/26   train_loss = 0.163
Epoch  41 Batch    6/26   train_loss = 0.176
Epoch  41 Batch    7/26   train_loss = 0.183
Epoch  41 Batch    8/26   train_loss = 0.169
Epoch  41 Batch    9/26   train_loss = 0.185
Epoch  41 Batch   10/26   train_loss = 0.169
Epoch  41 Batch   11/26   train_loss = 0.173
Epoch  41 Batch   12/26   train_loss = 0.179
Epoch  41 Batch   13/26   train_loss = 0.151
Epoch  41 Batch   14/26   train_loss = 0.186
Epoch  41 Batch   15/26   train_loss = 0.193
Epoch  41 Batch   16/26   train_loss = 0.189
Epoch  41 Batch   17/26   train_loss = 0.205
Epoch  41 Batch   18/26   train_loss = 0.178
Epoch  41 Batch   19/26   train_loss = 0.183
Epoch  41 Batch   20/26   train_loss = 0.176
Epoch  41 Batch   21/26   train_loss = 0.171
Epoch  41 Batch   22/26   train_loss = 0.180
Epoch  41 Batch   23/26   train_loss = 0.169
Epoch  41 Batch   24/26   train_loss = 0.176
Epoch  41 Batch   25/26   train_loss = 0.172
Epoch  42 Batch    0/26   train_loss = 0.182
Epoch  42 Batch    1/26   train_loss = 0.174
Epoch  42 Batch    2/26   train_loss = 0.188
Epoch  42 Batch    3/26   train_loss = 0.179
Epoch  42 Batch    4/26   train_loss = 0.200
Epoch  42 Batch    5/26   train_loss = 0.168
Epoch  42 Batch    6/26   train_loss = 0.174
Epoch  42 Batch    7/26   train_loss = 0.187
Epoch  42 Batch    8/26   train_loss = 0.174
Epoch  42 Batch    9/26   train_loss = 0.216
Epoch  42 Batch   10/26   train_loss = 0.168
Epoch  42 Batch   11/26   train_loss = 0.180
Epoch  42 Batch   12/26   train_loss = 0.193
Epoch  42 Batch   13/26   train_loss = 0.156
Epoch  42 Batch   14/26   train_loss = 0.185
Epoch  42 Batch   15/26   train_loss = 0.186
Epoch  42 Batch   16/26   train_loss = 0.190
Epoch  42 Batch   17/26   train_loss = 0.196
Epoch  42 Batch   18/26   train_loss = 0.182
Epoch  42 Batch   19/26   train_loss = 0.180
Epoch  42 Batch   20/26   train_loss = 0.180
Epoch  42 Batch   21/26   train_loss = 0.185
Epoch  42 Batch   22/26   train_loss = 0.188
Epoch  42 Batch   23/26   train_loss = 0.174
Epoch  42 Batch   24/26   train_loss = 0.171
Epoch  42 Batch   25/26   train_loss = 0.189
Epoch  43 Batch    0/26   train_loss = 0.193
Epoch  43 Batch    1/26   train_loss = 0.186
Epoch  43 Batch    2/26   train_loss = 0.184
Epoch  43 Batch    3/26   train_loss = 0.181
Epoch  43 Batch    4/26   train_loss = 0.198
Epoch  43 Batch    5/26   train_loss = 0.176
Epoch  43 Batch    6/26   train_loss = 0.179
Epoch  43 Batch    7/26   train_loss = 0.186
Epoch  43 Batch    8/26   train_loss = 0.173
Epoch  43 Batch    9/26   train_loss = 0.202
Epoch  43 Batch   10/26   train_loss = 0.171
Epoch  43 Batch   11/26   train_loss = 0.175
Epoch  43 Batch   12/26   train_loss = 0.186
Epoch  43 Batch   13/26   train_loss = 0.165
Epoch  43 Batch   14/26   train_loss = 0.194
Epoch  43 Batch   15/26   train_loss = 0.213
Epoch  43 Batch   16/26   train_loss = 0.194
Epoch  43 Batch   17/26   train_loss = 0.191
Epoch  43 Batch   18/26   train_loss = 0.180
Epoch  43 Batch   19/26   train_loss = 0.191
Epoch  43 Batch   20/26   train_loss = 0.185
Epoch  43 Batch   21/26   train_loss = 0.171
Epoch  43 Batch   22/26   train_loss = 0.184
Epoch  43 Batch   23/26   train_loss = 0.170
Epoch  43 Batch   24/26   train_loss = 0.213
Epoch  43 Batch   25/26   train_loss = 0.178
Epoch  44 Batch    0/26   train_loss = 0.201
Epoch  44 Batch    1/26   train_loss = 0.172
Epoch  44 Batch    2/26   train_loss = 0.189
Epoch  44 Batch    3/26   train_loss = 0.182
Epoch  44 Batch    4/26   train_loss = 0.205
Epoch  44 Batch    5/26   train_loss = 0.182
Epoch  44 Batch    6/26   train_loss = 0.176
Epoch  44 Batch    7/26   train_loss = 0.193
Epoch  44 Batch    8/26   train_loss = 0.174
Epoch  44 Batch    9/26   train_loss = 0.212
Epoch  44 Batch   10/26   train_loss = 0.171
Epoch  44 Batch   11/26   train_loss = 0.194
Epoch  44 Batch   12/26   train_loss = 0.194
Epoch  44 Batch   13/26   train_loss = 0.159
Epoch  44 Batch   14/26   train_loss = 0.188
Epoch  44 Batch   15/26   train_loss = 0.199
Epoch  44 Batch   16/26   train_loss = 0.199
Epoch  44 Batch   17/26   train_loss = 0.193
Epoch  44 Batch   18/26   train_loss = 0.198
Epoch  44 Batch   19/26   train_loss = 0.198
Epoch  44 Batch   20/26   train_loss = 0.195
Epoch  44 Batch   21/26   train_loss = 0.185
Epoch  44 Batch   22/26   train_loss = 0.176
Epoch  44 Batch   23/26   train_loss = 0.186
Epoch  44 Batch   24/26   train_loss = 0.189
Epoch  44 Batch   25/26   train_loss = 0.176
Epoch  45 Batch    0/26   train_loss = 0.220
Epoch  45 Batch    1/26   train_loss = 0.185
Epoch  45 Batch    2/26   train_loss = 0.194
Epoch  45 Batch    3/26   train_loss = 0.183
Epoch  45 Batch    4/26   train_loss = 0.232
Epoch  45 Batch    5/26   train_loss = 0.177
Epoch  45 Batch    6/26   train_loss = 0.183
Epoch  45 Batch    7/26   train_loss = 0.200
Epoch  45 Batch    8/26   train_loss = 0.169
Epoch  45 Batch    9/26   train_loss = 0.207
Epoch  45 Batch   10/26   train_loss = 0.202
Epoch  45 Batch   11/26   train_loss = 0.199
Epoch  45 Batch   12/26   train_loss = 0.198
Epoch  45 Batch   13/26   train_loss = 0.167
Epoch  45 Batch   14/26   train_loss = 0.210
Epoch  45 Batch   15/26   train_loss = 0.201
Epoch  45 Batch   16/26   train_loss = 0.213
Epoch  45 Batch   17/26   train_loss = 0.188
Epoch  45 Batch   18/26   train_loss = 0.226
Epoch  45 Batch   19/26   train_loss = 0.216
Epoch  45 Batch   20/26   train_loss = 0.192
Epoch  45 Batch   21/26   train_loss = 0.176
Epoch  45 Batch   22/26   train_loss = 0.189
Epoch  45 Batch   23/26   train_loss = 0.201
Epoch  45 Batch   24/26   train_loss = 0.208
Epoch  45 Batch   25/26   train_loss = 0.237
Epoch  46 Batch    0/26   train_loss = 0.247
Epoch  46 Batch    1/26   train_loss = 0.183
Epoch  46 Batch    2/26   train_loss = 0.210
Epoch  46 Batch    3/26   train_loss = 0.201
Epoch  46 Batch    4/26   train_loss = 0.256
Epoch  46 Batch    5/26   train_loss = 0.190
Epoch  46 Batch    6/26   train_loss = 0.196
Epoch  46 Batch    7/26   train_loss = 0.201
Epoch  46 Batch    8/26   train_loss = 0.184
Epoch  46 Batch    9/26   train_loss = 0.253
Epoch  46 Batch   10/26   train_loss = 0.199
Epoch  46 Batch   11/26   train_loss = 0.194
Epoch  46 Batch   12/26   train_loss = 0.223
Epoch  46 Batch   13/26   train_loss = 0.194
Epoch  46 Batch   14/26   train_loss = 0.220
Epoch  46 Batch   15/26   train_loss = 0.198
Epoch  46 Batch   16/26   train_loss = 0.216
Epoch  46 Batch   17/26   train_loss = 0.213
Epoch  46 Batch   18/26   train_loss = 0.249
Epoch  46 Batch   19/26   train_loss = 0.275
Epoch  46 Batch   20/26   train_loss = 0.230
Epoch  46 Batch   21/26   train_loss = 0.228
Epoch  46 Batch   22/26   train_loss = 0.190
Epoch  46 Batch   23/26   train_loss = 0.233
Epoch  46 Batch   24/26   train_loss = 0.216
Epoch  46 Batch   25/26   train_loss = 0.228
Epoch  47 Batch    0/26   train_loss = 0.226
Epoch  47 Batch    1/26   train_loss = 0.207
Epoch  47 Batch    2/26   train_loss = 0.231
Epoch  47 Batch    3/26   train_loss = 0.250
Epoch  47 Batch    4/26   train_loss = 0.261
Epoch  47 Batch    5/26   train_loss = 0.213
Epoch  47 Batch    6/26   train_loss = 0.238
Epoch  47 Batch    7/26   train_loss = 0.222
Epoch  47 Batch    8/26   train_loss = 0.218
Epoch  47 Batch    9/26   train_loss = 0.261
Epoch  47 Batch   10/26   train_loss = 0.291
Epoch  47 Batch   11/26   train_loss = 0.280
Epoch  47 Batch   12/26   train_loss = 0.239
Epoch  47 Batch   13/26   train_loss = 0.216
Epoch  47 Batch   14/26   train_loss = 0.272
Epoch  47 Batch   15/26   train_loss = 0.246
Epoch  47 Batch   16/26   train_loss = 0.265
Epoch  47 Batch   17/26   train_loss = 0.282
Epoch  47 Batch   18/26   train_loss = 0.282
Epoch  47 Batch   19/26   train_loss = 0.263
Epoch  47 Batch   20/26   train_loss = 0.235
Epoch  47 Batch   21/26   train_loss = 0.277
Epoch  47 Batch   22/26   train_loss = 0.246
Epoch  47 Batch   23/26   train_loss = 0.297
Epoch  47 Batch   24/26   train_loss = 0.251
Epoch  47 Batch   25/26   train_loss = 0.259
Epoch  48 Batch    0/26   train_loss = 0.264
Epoch  48 Batch    1/26   train_loss = 0.267
Epoch  48 Batch    2/26   train_loss = 0.301
Epoch  48 Batch    3/26   train_loss = 0.324
Epoch  48 Batch    4/26   train_loss = 0.270
Epoch  48 Batch    5/26   train_loss = 0.292
Epoch  48 Batch    6/26   train_loss = 0.275
Epoch  48 Batch    7/26   train_loss = 0.267
Epoch  48 Batch    8/26   train_loss = 0.234
Epoch  48 Batch    9/26   train_loss = 0.303
Epoch  48 Batch   10/26   train_loss = 0.345
Epoch  48 Batch   11/26   train_loss = 0.312
Epoch  48 Batch   12/26   train_loss = 0.282
Epoch  48 Batch   13/26   train_loss = 0.267
Epoch  48 Batch   14/26   train_loss = 0.319
Epoch  48 Batch   15/26   train_loss = 0.277
Epoch  48 Batch   16/26   train_loss = 0.323
Epoch  48 Batch   17/26   train_loss = 0.334
Epoch  48 Batch   18/26   train_loss = 0.352
Epoch  48 Batch   19/26   train_loss = 0.343
Epoch  48 Batch   20/26   train_loss = 0.291
Epoch  48 Batch   21/26   train_loss = 0.335
Epoch  48 Batch   22/26   train_loss = 0.353
Epoch  48 Batch   23/26   train_loss = 0.347
Epoch  48 Batch   24/26   train_loss = 0.304
Epoch  48 Batch   25/26   train_loss = 0.355
Epoch  49 Batch    0/26   train_loss = 0.340
Epoch  49 Batch    1/26   train_loss = 0.358
Epoch  49 Batch    2/26   train_loss = 0.344
Epoch  49 Batch    3/26   train_loss = 0.344
Epoch  49 Batch    4/26   train_loss = 0.333
Epoch  49 Batch    5/26   train_loss = 0.334
Epoch  49 Batch    6/26   train_loss = 0.349
Epoch  49 Batch    7/26   train_loss = 0.340
Epoch  49 Batch    8/26   train_loss = 0.297
Epoch  49 Batch    9/26   train_loss = 0.381
Epoch  49 Batch   10/26   train_loss = 0.383
Epoch  49 Batch   11/26   train_loss = 0.349
Epoch  49 Batch   12/26   train_loss = 0.374
Epoch  49 Batch   13/26   train_loss = 0.331
Epoch  49 Batch   14/26   train_loss = 0.373
Epoch  49 Batch   15/26   train_loss = 0.386
Epoch  49 Batch   16/26   train_loss = 0.393
Epoch  49 Batch   17/26   train_loss = 0.353
Epoch  49 Batch   18/26   train_loss = 0.384
Epoch  49 Batch   19/26   train_loss = 0.427
Epoch  49 Batch   20/26   train_loss = 0.413
Epoch  49 Batch   21/26   train_loss = 0.363
Epoch  49 Batch   22/26   train_loss = 0.388
Epoch  49 Batch   23/26   train_loss = 0.376
Epoch  49 Batch   24/26   train_loss = 0.359
Epoch  49 Batch   25/26   train_loss = 0.383
Epoch  50 Batch    0/26   train_loss = 0.345
Epoch  50 Batch    1/26   train_loss = 0.355
Epoch  50 Batch    2/26   train_loss = 0.403
Epoch  50 Batch    3/26   train_loss = 0.364
Epoch  50 Batch    4/26   train_loss = 0.394
Epoch  50 Batch    5/26   train_loss = 0.368
Epoch  50 Batch    6/26   train_loss = 0.367
Epoch  50 Batch    7/26   train_loss = 0.394
Epoch  50 Batch    8/26   train_loss = 0.341
Epoch  50 Batch    9/26   train_loss = 0.356
Epoch  50 Batch   10/26   train_loss = 0.369
Epoch  50 Batch   11/26   train_loss = 0.333
Epoch  50 Batch   12/26   train_loss = 0.394
Epoch  50 Batch   13/26   train_loss = 0.324
Epoch  50 Batch   14/26   train_loss = 0.356
Epoch  50 Batch   15/26   train_loss = 0.399
Epoch  50 Batch   16/26   train_loss = 0.406
Epoch  50 Batch   17/26   train_loss = 0.372
Epoch  50 Batch   18/26   train_loss = 0.333
Epoch  50 Batch   19/26   train_loss = 0.386
Epoch  50 Batch   20/26   train_loss = 0.365
Epoch  50 Batch   21/26   train_loss = 0.358
Epoch  50 Batch   22/26   train_loss = 0.358
Epoch  50 Batch   23/26   train_loss = 0.356
Epoch  50 Batch   24/26   train_loss = 0.301
Epoch  50 Batch   25/26   train_loss = 0.326
Epoch  51 Batch    0/26   train_loss = 0.321
Epoch  51 Batch    1/26   train_loss = 0.299
Epoch  51 Batch    2/26   train_loss = 0.348
Epoch  51 Batch    3/26   train_loss = 0.344
Epoch  51 Batch    4/26   train_loss = 0.358
Epoch  51 Batch    5/26   train_loss = 0.344
Epoch  51 Batch    6/26   train_loss = 0.341
Epoch  51 Batch    7/26   train_loss = 0.344
Epoch  51 Batch    8/26   train_loss = 0.287
Epoch  51 Batch    9/26   train_loss = 0.310
Epoch  51 Batch   10/26   train_loss = 0.317
Epoch  51 Batch   11/26   train_loss = 0.340
Epoch  51 Batch   12/26   train_loss = 0.333
Epoch  51 Batch   13/26   train_loss = 0.284
Epoch  51 Batch   14/26   train_loss = 0.350
Epoch  51 Batch   15/26   train_loss = 0.358
Epoch  51 Batch   16/26   train_loss = 0.355
Epoch  51 Batch   17/26   train_loss = 0.302
Epoch  51 Batch   18/26   train_loss = 0.305
Epoch  51 Batch   19/26   train_loss = 0.334
Epoch  51 Batch   20/26   train_loss = 0.298
Epoch  51 Batch   21/26   train_loss = 0.316
Epoch  51 Batch   22/26   train_loss = 0.280
Epoch  51 Batch   23/26   train_loss = 0.289
Epoch  51 Batch   24/26   train_loss = 0.254
Epoch  51 Batch   25/26   train_loss = 0.256
Epoch  52 Batch    0/26   train_loss = 0.274
Epoch  52 Batch    1/26   train_loss = 0.252
Epoch  52 Batch    2/26   train_loss = 0.307
Epoch  52 Batch    3/26   train_loss = 0.289
Epoch  52 Batch    4/26   train_loss = 0.288
Epoch  52 Batch    5/26   train_loss = 0.285
Epoch  52 Batch    6/26   train_loss = 0.266
Epoch  52 Batch    7/26   train_loss = 0.305
Epoch  52 Batch    8/26   train_loss = 0.255
Epoch  52 Batch    9/26   train_loss = 0.239
Epoch  52 Batch   10/26   train_loss = 0.251
Epoch  52 Batch   11/26   train_loss = 0.319
Epoch  52 Batch   12/26   train_loss = 0.253
Epoch  52 Batch   13/26   train_loss = 0.254
Epoch  52 Batch   14/26   train_loss = 0.319
Epoch  52 Batch   15/26   train_loss = 0.288
Epoch  52 Batch   16/26   train_loss = 0.275
Epoch  52 Batch   17/26   train_loss = 0.257
Epoch  52 Batch   18/26   train_loss = 0.279
Epoch  52 Batch   19/26   train_loss = 0.256
Epoch  52 Batch   20/26   train_loss = 0.265
Epoch  52 Batch   21/26   train_loss = 0.240
Epoch  52 Batch   22/26   train_loss = 0.220
Epoch  52 Batch   23/26   train_loss = 0.237
Epoch  52 Batch   24/26   train_loss = 0.224
Epoch  52 Batch   25/26   train_loss = 0.209
Epoch  53 Batch    0/26   train_loss = 0.264
Epoch  53 Batch    1/26   train_loss = 0.216
Epoch  53 Batch    2/26   train_loss = 0.258
Epoch  53 Batch    3/26   train_loss = 0.217
Epoch  53 Batch    4/26   train_loss = 0.266
Epoch  53 Batch    5/26   train_loss = 0.243
Epoch  53 Batch    6/26   train_loss = 0.227
Epoch  53 Batch    7/26   train_loss = 0.255
Epoch  53 Batch    8/26   train_loss = 0.215
Epoch  53 Batch    9/26   train_loss = 0.212
Epoch  53 Batch   10/26   train_loss = 0.201
Epoch  53 Batch   11/26   train_loss = 0.251
Epoch  53 Batch   12/26   train_loss = 0.231
Epoch  53 Batch   13/26   train_loss = 0.202
Epoch  53 Batch   14/26   train_loss = 0.242
Epoch  53 Batch   15/26   train_loss = 0.227
Epoch  53 Batch   16/26   train_loss = 0.241
Epoch  53 Batch   17/26   train_loss = 0.237
Epoch  53 Batch   18/26   train_loss = 0.209
Epoch  53 Batch   19/26   train_loss = 0.236
Epoch  53 Batch   20/26   train_loss = 0.241
Epoch  53 Batch   21/26   train_loss = 0.208
Epoch  53 Batch   22/26   train_loss = 0.199
Epoch  53 Batch   23/26   train_loss = 0.205
Epoch  53 Batch   24/26   train_loss = 0.188
Epoch  53 Batch   25/26   train_loss = 0.187
Epoch  54 Batch    0/26   train_loss = 0.214
Epoch  54 Batch    1/26   train_loss = 0.197
Epoch  54 Batch    2/26   train_loss = 0.219
Epoch  54 Batch    3/26   train_loss = 0.198
Epoch  54 Batch    4/26   train_loss = 0.224
Epoch  54 Batch    5/26   train_loss = 0.192
Epoch  54 Batch    6/26   train_loss = 0.204
Epoch  54 Batch    7/26   train_loss = 0.216
Epoch  54 Batch    8/26   train_loss = 0.182
Epoch  54 Batch    9/26   train_loss = 0.196
Epoch  54 Batch   10/26   train_loss = 0.197
Epoch  54 Batch   11/26   train_loss = 0.206
Epoch  54 Batch   12/26   train_loss = 0.208
Epoch  54 Batch   13/26   train_loss = 0.169
Epoch  54 Batch   14/26   train_loss = 0.212
Epoch  54 Batch   15/26   train_loss = 0.207
Epoch  54 Batch   16/26   train_loss = 0.212
Epoch  54 Batch   17/26   train_loss = 0.203
Epoch  54 Batch   18/26   train_loss = 0.199
Epoch  54 Batch   19/26   train_loss = 0.211
Epoch  54 Batch   20/26   train_loss = 0.194
Epoch  54 Batch   21/26   train_loss = 0.188
Epoch  54 Batch   22/26   train_loss = 0.184
Epoch  54 Batch   23/26   train_loss = 0.185
Epoch  54 Batch   24/26   train_loss = 0.193
Epoch  54 Batch   25/26   train_loss = 0.174
Epoch  55 Batch    0/26   train_loss = 0.192
Epoch  55 Batch    1/26   train_loss = 0.194
Epoch  55 Batch    2/26   train_loss = 0.200
Epoch  55 Batch    3/26   train_loss = 0.185
Epoch  55 Batch    4/26   train_loss = 0.211
Epoch  55 Batch    5/26   train_loss = 0.181
Epoch  55 Batch    6/26   train_loss = 0.193
Epoch  55 Batch    7/26   train_loss = 0.190
Epoch  55 Batch    8/26   train_loss = 0.174
Epoch  55 Batch    9/26   train_loss = 0.192
Epoch  55 Batch   10/26   train_loss = 0.178
Epoch  55 Batch   11/26   train_loss = 0.180
Epoch  55 Batch   12/26   train_loss = 0.185
Epoch  55 Batch   13/26   train_loss = 0.163
Epoch  55 Batch   14/26   train_loss = 0.196
Epoch  55 Batch   15/26   train_loss = 0.202
Epoch  55 Batch   16/26   train_loss = 0.194
Epoch  55 Batch   17/26   train_loss = 0.193
Epoch  55 Batch   18/26   train_loss = 0.183
Epoch  55 Batch   19/26   train_loss = 0.196
Epoch  55 Batch   20/26   train_loss = 0.180
Epoch  55 Batch   21/26   train_loss = 0.178
Epoch  55 Batch   22/26   train_loss = 0.174
Epoch  55 Batch   23/26   train_loss = 0.184
Epoch  55 Batch   24/26   train_loss = 0.173
Epoch  55 Batch   25/26   train_loss = 0.171
Epoch  56 Batch    0/26   train_loss = 0.184
Epoch  56 Batch    1/26   train_loss = 0.174
Epoch  56 Batch    2/26   train_loss = 0.190
Epoch  56 Batch    3/26   train_loss = 0.188
Epoch  56 Batch    4/26   train_loss = 0.202
Epoch  56 Batch    5/26   train_loss = 0.174
Epoch  56 Batch    6/26   train_loss = 0.183
Epoch  56 Batch    7/26   train_loss = 0.189
Epoch  56 Batch    8/26   train_loss = 0.172
Epoch  56 Batch    9/26   train_loss = 0.180
Epoch  56 Batch   10/26   train_loss = 0.167
Epoch  56 Batch   11/26   train_loss = 0.181
Epoch  56 Batch   12/26   train_loss = 0.182
Epoch  56 Batch   13/26   train_loss = 0.152
Epoch  56 Batch   14/26   train_loss = 0.186
Epoch  56 Batch   15/26   train_loss = 0.189
Epoch  56 Batch   16/26   train_loss = 0.196
Epoch  56 Batch   17/26   train_loss = 0.192
Epoch  56 Batch   18/26   train_loss = 0.184
Epoch  56 Batch   19/26   train_loss = 0.181
Epoch  56 Batch   20/26   train_loss = 0.174
Epoch  56 Batch   21/26   train_loss = 0.177
Epoch  56 Batch   22/26   train_loss = 0.172
Epoch  56 Batch   23/26   train_loss = 0.172
Epoch  56 Batch   24/26   train_loss = 0.183
Epoch  56 Batch   25/26   train_loss = 0.165
Epoch  57 Batch    0/26   train_loss = 0.178
Epoch  57 Batch    1/26   train_loss = 0.181
Epoch  57 Batch    2/26   train_loss = 0.186
Epoch  57 Batch    3/26   train_loss = 0.179
Epoch  57 Batch    4/26   train_loss = 0.198
Epoch  57 Batch    5/26   train_loss = 0.165
Epoch  57 Batch    6/26   train_loss = 0.174
Epoch  57 Batch    7/26   train_loss = 0.182
Epoch  57 Batch    8/26   train_loss = 0.164
Epoch  57 Batch    9/26   train_loss = 0.185
Epoch  57 Batch   10/26   train_loss = 0.175
Epoch  57 Batch   11/26   train_loss = 0.173
Epoch  57 Batch   12/26   train_loss = 0.178
Epoch  57 Batch   13/26   train_loss = 0.151
Epoch  57 Batch   14/26   train_loss = 0.184
Epoch  57 Batch   15/26   train_loss = 0.191
Epoch  57 Batch   16/26   train_loss = 0.186
Epoch  57 Batch   17/26   train_loss = 0.178
Epoch  57 Batch   18/26   train_loss = 0.179
Epoch  57 Batch   19/26   train_loss = 0.183
Epoch  57 Batch   20/26   train_loss = 0.174
Epoch  57 Batch   21/26   train_loss = 0.172
Epoch  57 Batch   22/26   train_loss = 0.171
Epoch  57 Batch   23/26   train_loss = 0.167
Epoch  57 Batch   24/26   train_loss = 0.170
Epoch  57 Batch   25/26   train_loss = 0.162
Epoch  58 Batch    0/26   train_loss = 0.180
Epoch  58 Batch    1/26   train_loss = 0.173
Epoch  58 Batch    2/26   train_loss = 0.187
Epoch  58 Batch    3/26   train_loss = 0.180
Epoch  58 Batch    4/26   train_loss = 0.191
Epoch  58 Batch    5/26   train_loss = 0.167
Epoch  58 Batch    6/26   train_loss = 0.174
Epoch  58 Batch    7/26   train_loss = 0.187
Epoch  58 Batch    8/26   train_loss = 0.161
Epoch  58 Batch    9/26   train_loss = 0.178
Epoch  58 Batch   10/26   train_loss = 0.171
Epoch  58 Batch   11/26   train_loss = 0.177
Epoch  58 Batch   12/26   train_loss = 0.182
Epoch  58 Batch   13/26   train_loss = 0.149
Epoch  58 Batch   14/26   train_loss = 0.181
Epoch  58 Batch   15/26   train_loss = 0.184
Epoch  58 Batch   16/26   train_loss = 0.191
Epoch  58 Batch   17/26   train_loss = 0.183
Epoch  58 Batch   18/26   train_loss = 0.178
Epoch  58 Batch   19/26   train_loss = 0.178
Epoch  58 Batch   20/26   train_loss = 0.173
Epoch  58 Batch   21/26   train_loss = 0.174
Epoch  58 Batch   22/26   train_loss = 0.171
Epoch  58 Batch   23/26   train_loss = 0.168
Epoch  58 Batch   24/26   train_loss = 0.172
Epoch  58 Batch   25/26   train_loss = 0.161
Epoch  59 Batch    0/26   train_loss = 0.174
Epoch  59 Batch    1/26   train_loss = 0.175
Epoch  59 Batch    2/26   train_loss = 0.184
Epoch  59 Batch    3/26   train_loss = 0.175
Epoch  59 Batch    4/26   train_loss = 0.192
Epoch  59 Batch    5/26   train_loss = 0.162
Epoch  59 Batch    6/26   train_loss = 0.172
Epoch  59 Batch    7/26   train_loss = 0.179
Epoch  59 Batch    8/26   train_loss = 0.161
Epoch  59 Batch    9/26   train_loss = 0.181
Epoch  59 Batch   10/26   train_loss = 0.168
Epoch  59 Batch   11/26   train_loss = 0.171
Epoch  59 Batch   12/26   train_loss = 0.176
Epoch  59 Batch   13/26   train_loss = 0.147
Epoch  59 Batch   14/26   train_loss = 0.181
Epoch  59 Batch   15/26   train_loss = 0.187
Epoch  59 Batch   16/26   train_loss = 0.183
Epoch  59 Batch   17/26   train_loss = 0.174
Epoch  59 Batch   18/26   train_loss = 0.174
Epoch  59 Batch   19/26   train_loss = 0.180
Epoch  59 Batch   20/26   train_loss = 0.172
Epoch  59 Batch   21/26   train_loss = 0.170
Epoch  59 Batch   22/26   train_loss = 0.170
Epoch  59 Batch   23/26   train_loss = 0.166
Epoch  59 Batch   24/26   train_loss = 0.168
Epoch  59 Batch   25/26   train_loss = 0.163
Model Trained and Saved
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Save Parameters[¶](#Save-Parameters)

Save `seq_length` and `save_dir` for generating a new TV script.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [74]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="c1"># Save parameters for checkpoint</span>
<span class="n">helper</span><span class="o">.</span><span class="n">save_params</span><span class="p">((</span><span class="n">seq_length</span><span class="p">,</span> <span class="n">save_dir</span><span class="p">))</span>
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

# Checkpoint[¶](#Checkpoint)

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [117]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL</span>
<span class="sd">"""</span>
<span class="kn">import</span> <span class="nn">tensorflow</span> <span class="k">as</span> <span class="nn">tf</span>
<span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>
<span class="kn">import</span> <span class="nn">helper</span>
<span class="kn">import</span> <span class="nn">problem_unittests</span> <span class="k">as</span> <span class="nn">tests</span>

<span class="n">_</span><span class="p">,</span> <span class="n">vocab_to_int</span><span class="p">,</span> <span class="n">int_to_vocab</span><span class="p">,</span> <span class="n">token_dict</span> <span class="o">=</span> <span class="n">helper</span><span class="o">.</span><span class="n">load_preprocess</span><span class="p">()</span>
<span class="n">seq_length</span><span class="p">,</span> <span class="n">load_dir</span> <span class="o">=</span> <span class="n">helper</span><span class="o">.</span><span class="n">load_params</span><span class="p">()</span>
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Implement Generate Functions[¶](#Implement-Generate-Functions)

### Get Tensors[¶](#Get-Tensors)

Get tensors from `loaded_graph` using the function [`get_tensor_by_name()`](https://www.tensorflow.org/api_docs/python/tf/Graph#get_tensor_by_name). Get the tensors using the following names:

*   "input:0"
*   "initial_state:0"
*   "final_state:0"
*   "probs:0"

Return the tensors in the following tuple `(InputTensor, InitialStateTensor, FinalStateTensor, ProbsTensor)`

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [118]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">get_tensors</span><span class="p">(</span><span class="n">loaded_graph</span><span class="p">):</span>
    <span class="sd">"""</span>
 <span class="sd">Get input, initial state, final state, and probabilities tensor from <loaded_graph></span>
 <span class="sd">:param loaded_graph: TensorFlow graph loaded from file</span>
 <span class="sd">:return: Tuple (InputTensor, InitialStateTensor, FinalStateTensor, ProbsTensor)</span>
 <span class="sd">"""</span>
    <span class="k">with</span> <span class="n">loaded_graph</span><span class="o">.</span><span class="n">as_default</span><span class="p">():</span>
        <span class="n">InputTensor</span> <span class="o">=</span> <span class="n">loaded_graph</span><span class="o">.</span><span class="n">get_tensor_by_name</span><span class="p">(</span><span class="s2">"input:0"</span><span class="p">)</span>
        <span class="n">InitialStateTensor</span> <span class="o">=</span> <span class="n">loaded_graph</span><span class="o">.</span><span class="n">get_tensor_by_name</span><span class="p">(</span><span class="s2">"initial_state:0"</span><span class="p">)</span>
        <span class="n">FinalStateTensor</span> <span class="o">=</span> <span class="n">loaded_graph</span><span class="o">.</span><span class="n">get_tensor_by_name</span><span class="p">(</span><span class="s2">"final_state:0"</span><span class="p">)</span>
        <span class="n">ProbsTensor</span> <span class="o">=</span> <span class="n">loaded_graph</span><span class="o">.</span><span class="n">get_tensor_by_name</span><span class="p">(</span><span class="s2">"probs:0"</span><span class="p">)</span>

    <span class="c1"># TODO: Implement Function</span>
    <span class="k">return</span> <span class="n">InputTensor</span><span class="p">,</span> <span class="n">InitialStateTensor</span><span class="p">,</span> <span class="n">FinalStateTensor</span><span class="p">,</span> <span class="n">ProbsTensor</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_get_tensors</span><span class="p">(</span><span class="n">get_tensors</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

### Choose Word[¶](#Choose-Word)

Implement the `pick_word()` function to select the next word using `probabilities`.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [155]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="k">def</span> <span class="nf">pick_word</span><span class="p">(</span><span class="n">probabilities</span><span class="p">,</span> <span class="n">int_to_vocab</span><span class="p">):</span>
    <span class="sd">"""</span>
 <span class="sd">Pick the next word in the generated text</span>
 <span class="sd">:param probabilities: Probabilites of the next word</span>
 <span class="sd">:param int_to_vocab: Dictionary of word ids as the keys and words as the values</span>
 <span class="sd">:return: String of the predicted word</span>
 <span class="sd">"""</span>
    <span class="c1"># TODO: Implement Function</span>
    <span class="n">predword</span> <span class="o">=</span> <span class="nb">int</span><span class="p">(</span><span class="n">np</span><span class="o">.</span><span class="n">random</span><span class="o">.</span><span class="n">choice</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">probabilities</span><span class="p">),</span><span class="mi">1</span><span class="p">,</span><span class="n">p</span><span class="o">=</span><span class="n">probabilities</span><span class="p">))</span>
    <span class="n">predword</span> <span class="o">=</span> <span class="n">int_to_vocab</span><span class="p">[</span><span class="n">predword</span><span class="p">]</span>
    <span class="k">return</span> <span class="n">predword</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">tests</span><span class="o">.</span><span class="n">test_pick_word</span><span class="p">(</span><span class="n">pick_word</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>Tests Passed
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

## Generate TV Script[¶](#Generate-TV-Script)

This will generate the TV script for you. Set `gen_length` to the length of TV script you want to generate.

</div>

</div>

</div>

<div class="cell border-box-sizing code_cell rendered">

<div class="input">

<div class="prompt input_prompt">In [156]:</div>

<div class="inner_cell">

<div class="input_area">

<div class=" highlight hl-ipython3">

<pre><span></span><span class="n">gen_length</span> <span class="o">=</span> <span class="mi">200</span>
<span class="c1"># homer_simpson, moe_szyslak, or Barney_Gumble</span>
<span class="n">prime_word</span> <span class="o">=</span> <span class="s1">'moe_szyslak'</span>

<span class="sd">"""</span>
<span class="sd">DON'T MODIFY ANYTHING IN THIS CELL THAT IS BELOW THIS LINE</span>
<span class="sd">"""</span>
<span class="n">loaded_graph</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">Graph</span><span class="p">()</span>
<span class="k">with</span> <span class="n">tf</span><span class="o">.</span><span class="n">Session</span><span class="p">(</span><span class="n">graph</span><span class="o">=</span><span class="n">loaded_graph</span><span class="p">)</span> <span class="k">as</span> <span class="n">sess</span><span class="p">:</span>
    <span class="c1"># Load saved model</span>
    <span class="n">loader</span> <span class="o">=</span> <span class="n">tf</span><span class="o">.</span><span class="n">train</span><span class="o">.</span><span class="n">import_meta_graph</span><span class="p">(</span><span class="n">load_dir</span> <span class="o">+</span> <span class="s1">'.meta'</span><span class="p">)</span>
    <span class="n">loader</span><span class="o">.</span><span class="n">restore</span><span class="p">(</span><span class="n">sess</span><span class="p">,</span> <span class="n">load_dir</span><span class="p">)</span>
    <span class="c1"># Get Tensors from loaded model</span>
    <span class="n">input_text</span><span class="p">,</span> <span class="n">initial_state</span><span class="p">,</span> <span class="n">final_state</span><span class="p">,</span> <span class="n">probs</span> <span class="o">=</span> <span class="n">get_tensors</span><span class="p">(</span><span class="n">loaded_graph</span><span class="p">)</span>

    <span class="c1"># Sentences generation setup</span>
    <span class="n">gen_sentences</span> <span class="o">=</span> <span class="p">[</span><span class="n">prime_word</span> <span class="o">+</span> <span class="s1">':'</span><span class="p">]</span>
    <span class="n">prev_state</span> <span class="o">=</span> <span class="n">sess</span><span class="o">.</span><span class="n">run</span><span class="p">(</span><span class="n">initial_state</span><span class="p">,</span> <span class="p">{</span><span class="n">input_text</span><span class="p">:</span> <span class="n">np</span><span class="o">.</span><span class="n">array</span><span class="p">([[</span><span class="mi">1</span><span class="p">]])})</span>

    <span class="c1"># Generate sentences</span>
    <span class="k">for</span> <span class="n">n</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="n">gen_length</span><span class="p">):</span>
        <span class="c1"># Dynamic Input</span>
        <span class="n">dyn_input</span> <span class="o">=</span> <span class="p">[[</span><span class="n">vocab_to_int</span><span class="p">[</span><span class="n">word</span><span class="p">]</span> <span class="k">for</span> <span class="n">word</span> <span class="ow">in</span> <span class="n">gen_sentences</span><span class="p">[</span><span class="o">-</span><span class="n">seq_length</span><span class="p">:]]]</span>
        <span class="n">dyn_seq_length</span> <span class="o">=</span> <span class="nb">len</span><span class="p">(</span><span class="n">dyn_input</span><span class="p">[</span><span class="mi">0</span><span class="p">])</span>

        <span class="c1"># Get Prediction</span>
        <span class="n">probabilities</span><span class="p">,</span> <span class="n">prev_state</span> <span class="o">=</span> <span class="n">sess</span><span class="o">.</span><span class="n">run</span><span class="p">(</span>
            <span class="p">[</span><span class="n">probs</span><span class="p">,</span> <span class="n">final_state</span><span class="p">],</span>
            <span class="p">{</span><span class="n">input_text</span><span class="p">:</span> <span class="n">dyn_input</span><span class="p">,</span> <span class="n">initial_state</span><span class="p">:</span> <span class="n">prev_state</span><span class="p">})</span>

        <span class="n">pred_word</span> <span class="o">=</span> <span class="n">pick_word</span><span class="p">(</span><span class="n">probabilities</span><span class="p">[</span><span class="n">dyn_seq_length</span><span class="o">-</span><span class="mi">1</span><span class="p">],</span> <span class="n">int_to_vocab</span><span class="p">)</span>
        <span class="n">gen_sentences</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">pred_word</span><span class="p">)</span>

    <span class="c1"># Remove tokens</span>
    <span class="n">tv_script</span> <span class="o">=</span> <span class="s1">' '</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="n">gen_sentences</span><span class="p">)</span>
    <span class="k">for</span> <span class="n">key</span><span class="p">,</span> <span class="n">token</span> <span class="ow">in</span> <span class="n">token_dict</span><span class="o">.</span><span class="n">items</span><span class="p">():</span>
        <span class="n">ending</span> <span class="o">=</span> <span class="s1">' '</span> <span class="k">if</span> <span class="n">key</span> <span class="ow">in</span> <span class="p">[</span><span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="p">,</span> <span class="s1">'('</span><span class="p">,</span> <span class="s1">'"'</span><span class="p">]</span> <span class="k">else</span> <span class="s1">''</span>
        <span class="n">tv_script</span> <span class="o">=</span> <span class="n">tv_script</span><span class="o">.</span><span class="n">replace</span><span class="p">(</span><span class="s1">' '</span> <span class="o">+</span> <span class="n">token</span><span class="o">.</span><span class="n">lower</span><span class="p">(),</span> <span class="n">key</span><span class="p">)</span>
    <span class="n">tv_script</span> <span class="o">=</span> <span class="n">tv_script</span><span class="o">.</span><span class="n">replace</span><span class="p">(</span><span class="s1">'</span><span class="se">\n</span> <span class="s1">'</span><span class="p">,</span> <span class="s1">'</span><span class="se">\n</span><span class="s1">'</span><span class="p">)</span>
    <span class="n">tv_script</span> <span class="o">=</span> <span class="n">tv_script</span><span class="o">.</span><span class="n">replace</span><span class="p">(</span><span class="s1">'( '</span><span class="p">,</span> <span class="s1">'('</span><span class="p">)</span>

    <span class="nb">print</span><span class="p">(</span><span class="n">tv_script</span><span class="p">)</span>
</pre>

</div>

</div>

</div>

</div>

<div class="output_wrapper">

<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">

<pre>moe_szyslak:(delighted) ooh, a text.(checks phone) let's see... text message for the rest of freedom.
kent_brockman:(threatening) well, homer, i thought you said that.
moe_szyslak:(married) ah, good is.
ned_flanders: it is the" code" it's coat ninety-nine", what'll" harvey it."
moe_szyslak: no, you got the wrong guy.
homer_simpson:(awkwardly) you dropped somethin'.
moe_szyslak: c'mon, i'm sorry, uncle. experiments, i didn't close up my day...(homer) i should be mad at it, moe.(gun) whoa, whoa, whoa. you said no fuss.
health_inspector: here, i'm looking for my viva here... which means"...
moe_szyslak:... the last angry clown. / canyonero is this hourly with a wheel") hold the sensible fire, truck.

carny: big hole, when are we gonna do it, moe.
moe_szyslak:(low
</pre>

</div>

</div>

</div>

</div>

</div>

<div class="cell border-box-sizing text_cell rendered">

<div class="inner_cell">

<div class="text_cell_render border-box-sizing rendered_html">

# The TV Script is Nonsensical[¶](#The-TV-Script-is-Nonsensical)

It's ok if the TV script doesn't make any sense. We trained on less than a megabyte of text. In order to get good results, you'll have to use a smaller vocabulary or get more data. Luckily there's more data! As we mentioned in the beggining of this project, this is a subset of [another dataset](https://www.kaggle.com/wcukierski/the-simpsons-by-the-data). We didn't have you train on all the data, because that would take too long. However, you are free to train your neural network on all the data. After you complete the project, of course.

# Submitting This Project[¶](#Submitting-This-Project)

When submitting this project, make sure to run all the cells before saving the notebook. Save the notebook file as "dlnd_tv_script_generation.ipynb" and save it as a HTML file under "File" -> "Download as". Include the "helper.py" and "problem_unittests.py" files in your submission.

</div>

</div>

</div>

</div>

</div>
