- ChatGPT, Claude, Gemini etc are LLMs.
- LLMs stand for Large Language Model.
- LLMs are sophistaced AI models trained on large datasets to accept, analyze and generate content in human readable format.

- # GPT Architecture #
  - GPT stands for **GENERATIVE PRE-TRAINED TRANSFORMER**
    - GENERATIVE means it is generating something
    - PRE-TRAINED means it is generating something based on previously trained data
    - TRANSFORMER means reality, e.g. GPT, Gemini, claude etc are transformers based on certain pre- trained data that generates some output
  - Input messages are INPUT TOKENS & generated outputs are OUTPUT TOKENS.
  <img width="872" height="212" alt="image" src="https://github.com/user-attachments/assets/8274b89d-49a3-4244-bb0b-e6efbf66809d" />

  - For example, Input Token is "HI", then transformer will generate the next token [through ML datasets] which is ",". After that the input token will become "HI ," , then the transformer will generate the next token which is "H". This process will continue until there is no next token <END> in the dataset. After that the transformer will generate output " Hi, How can i help you?"

- # Fundamentals of Tokenization in NLP #
  - Tokenization is the process of breaking a sentence into smaller units called TOKENS.
  - Tokens can be words, sub-words, characters or symbols.
  - LLMs do not understand human language directly. They first convert the input text into tokens.
  - After tokenization, each token is converted into a numerical representation that the model can process.
  
  ## Examples
  
  - Sentence: "Hi, How are you?"
  
    Tokens:
    - "Hi"
    - ","
    - "How"
    - "are"
    - "you"
    - "?"
  
  - Word: "playing"
  
    Possible Tokens:
    - "play"
    - "ing"
  
  - Word: "unbelievable"
  
    Possible Tokens:
    - "un"
    - "believe"
    - "able"
  
  - During text generation, the model predicts one token at a time.
  - Each newly generated token is appended to the input sequence and used to predict the next token.
  - This process continues until an END token is reached.
  
  ## Why Tokenization is Important
  
  - Reduces vocabulary size.
  - Helps the model process text efficiently.
  - Enables handling of unknown or rare words.
  - Forms the foundation of how LLMs understand and generate language.
