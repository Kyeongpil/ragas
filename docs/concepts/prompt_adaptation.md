# Automatic prompt Adaptation

All the prompts used in ragas are natively written fully in English language and hence ragas natively may not work as expected when using with languages other than English. Automatic prompt adaptation is built to overcome this problem.

## How this is made possible?

```{note}
If you're unfamilar with Prompt object in ragas refer [Prompt Object](./prompts.md)
````
Each prompt in Ragas contains instructions and demonstrations. Through research and experiments, we found that by providing demonstrations in the target language can help LLMs adapt easily to any given target language. Leveraging this key insight we carefully translate all the relevant parts of the demonstrations into the target language. This is done using an LLM and once translated, the prompt can be saved to disk for reuse later.

**Example** 

Native prompt
```{code-block} python
Extract the noun from given sentence

sentence: "The sun sets over the mountains."
nouns: ["sun", "mountains"]

sentence: {sentence}
nouns:
```
Prompt adapted to Hindi
```{code-block} python
Extract the noun from the given sentence

sentence: "सूरज पहाड़ों के पार डूबता है।"
nouns: ["सूरज", "पहाड़"]

sentence: {sentence}
nouns:
```

### API Details

Create a sample prompt using `Prompt` class.

```{code-block} python

from langchain.chat_models import ChatOpenAI
from ragas.llms.prompt import Prompt

noun_extractor = Prompt(
name="noun_extractor",
instruction="Extract the noun from given sentence",
examples=[{
    "sentence":"The sun sets over the mountains.",
    "nouns":["sun", "mountains"]
}],
    input_keys=["sentence"],
    output_key="nouns",
    output_type="json"
)

openai_model = ChatOpenAI(model_name="gpt-4")
```

Prompt adaption is done using the `.adapt` method: 

`adapt(self, language, llm, cache_dir)`

The adapt method takes in a target language, LLM and adapts the prompts to the given target language. In case the adapted prompt is already present in `cache_dir` it is loaded.

```{code-block} python
noun_extractor.adapt(language="hindi",llm=openai_model)
```

```{code-block}
Prompt(name='noun_extractor', instruction='Extract the noun from given sentence', examples=[{'sentence': 'सूरज पहाड़ों के पार डूबता है।', 'nouns': ['सूरज', 'पहाड़']}], input_keys=['sentence'], output_key='nouns')
```

The quality of the adapted prompt depends on the quality of LLM, so we advise you to use the best you have with prompt adaptation.