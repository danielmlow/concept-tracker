# concept-tracker

"Like command-F on steroids"

[![Release](https://img.shields.io/github/v/release/danielmlow/concept-tracker)](https://img.shields.io/github/v/release/danielmlow/concept-tracker)
[![Build status](https://img.shields.io/github/actions/workflow/status/danielmlow/concept-tracker/main.yml?branch=main)](https://github.com/danielmlow/concept-tracker/actions/workflows/main.yml?query=branch%3Amain)
[![codecov](https://codecov.io/gh/danielmlow/concept-tracker/branch/main/graph/badge.svg)](https://codecov.io/gh/danielmlow/concept-tracker)
[![Commit activity](https://img.shields.io/github/commit-activity/m/danielmlow/concept-tracker)](https://img.shields.io/github/commit-activity/m/danielmlow/concept-tracker)
[![License](https://img.shields.io/github/license/danielmlow/concept-tracker)](https://img.shields.io/github/license/danielmlow/concept-tracker)

# Concept Tracker

- **Documentation** <https://danielmlow.github.io/concept-tracker/>

Concept Tracker is a tool for measuring concepts and psychological constructs in text by either:

- Analyzing documents with existing lexicons
- Generating a lexicon using Generative AI APIs ('gpt-4', 'gpt-3.5-turbo'). Similar to generating a thesauri but of a specific sense (which is not easy to extract from a thesauri)

# Installation

You can install in python via pip:

```
pip install concept-tracker
```

# Get API keys

- Gemini: https://makersuite.google.com/app/apikey

# Analyzing documents with existing lexicons

Then in a Python shell (e.g., Colab), run:

```python
from concept_tracker import Lexicon
lexicon = Lexicon(name = 'suicide_risk')
documents = ["I've been feeling sad and had a relapse"]
lexicon.extract(documents, normalize=True) # Normalize will divide concept count by document word count
{0: {
    'document': "I've been feeling sad and had a relapse",
    'abuse_physical': {'score': 0}

    ...
    'substance_use': 0.111111,  # 1 match / 9 words
    },
1:  {
    ...
    },
'matches_across_documents': {
    'abuse_physical': ()
    ...
    'substance_use': ('rellapse', 1)
    }
}
```

You can create a Pandas DataFrame as well:

```python
lexicon.to_pandas()
| id | document                           | abuse_physical | ... | substance_use |
|----|------------------------------------|----------------|-----|---------------|
| 0  | "I've been feeling sad and had a relapse" | 0              | ... | 0             |
```

Save your results. jsons can be opened in browsers for a prettier view.

```python
# add unique datetime to avoid overwriting files
import datetime
ts = datetime.datetime.utcnow().strftime('%y-%m-%dT%H-%M-%S')
filename = f'suicide_risk_analysis_{ts}'
lexicon.to_csv(f'path/to/{filename}.csv')
lexicon.to_json(f'path/to/{filename}.json')
```

# Generate lexicon

You can add a concept and definition to an existing lexicon and save as an csv. Setting definition argument to one of the APIs will have them generate it. Otherwise, you can add your own definition as a string.

```python
lexicon.create_concept('resilience')

'''
{'resilence':
    {
        'clean_name':'resilience',
        'definition': 'Resilience refers to the capacity of individuals to withstand or recover quickly from difficult situations, such as stress, trauma,...',
        'source': 'gpt-4',
        'tokens': ["resilience", "adaptability", "grit", "fortitude", "recovery", "bounce back", "perseverance", "mental toughness", "coping skills", "emotional strength", "overcoming adversity", "determination", "endurance", "tenacity", "inner strength", "survivor spirit", "stress management", "flexibility", "hardiness", "resilient mindset"]}
    }
}
'''

```

Or you can create your own lexicon from scratch

```python
lexicon = Lexicon()
lexicon.create_concept('resilience', save_lexicon = 'path/to/lexicon_ts.csv')
```

You can also save the lexicon without the analysis to add to your study supplementary documents

```python
lexicon.lexicon
# {'resilience':['resilience', 'adaptability', ...], }
filename = f'lexicon_resilience_{ts}'
lexicon.lexicon.to_csv(f'path/to/{filename}.csv')
lexicon.lexicon.to_json(f'path/to/{filename}.json')
```

# Lexicon validation

### Manual inspection of matches or positives

Even if a lexicon has been validated by others, it is important to check whether it is working well on your dataset. Let's inspect the how different tokens of the lexicon were found in your documents (i.e., matches or positives) and see how many are false positives.

```python
lexicon.matches()
```

Remove tokens for a given lexicon if it's creating too many false positives. In academic settings, report in your manuscript which tokens you removed.

```python
lexicon.remove_tokens(concept=, tokens=[])

```

You can also add tokens to an existing lexicon. Things to take into account:

- Minimal base form. When in doubt add both.
- lexicon.remove_tokens(concept, tokens=[])

### Criterion validity with expert judgements

Proper validation would require criterion validity; for instance, for a clinical lexicon making sure it matches a gold-standard criterion such as clinical experts judgements. We demonstrated for 40 constructs that expert clinicians on average removed X% of words generated by GPT-4 and they added N words missed by GPT-4. So if you use GPT-4 results without validation

```python
lexicon.lexicon.judgement_task()
| resilience   | resilience_keep | resilence_added | resilience_notes |
|--------------|-----------------|-----------------|------------------|
| resilience   | 4               | 0               |                  |
| adaptability | 2               | 0               |                  |
| grit         | 3               | 0               |                  |
lexicon.lexicon.judgement_task().to_csv(filename)
```

# Contextual information: negations, valence shifters,

<!-- "For example, Muhammad et al. (2016) modify the sentiment score output of their lexicon based on the proximity of negation words and valence shifters, and Vargas et al. (2021) con- struct a lexicon that explicitly defines words that are context-independent (i.e., will retain their meaning regardless of context) and context-dependent." Lin and Morency (2023) -->

# Using lexicons to explain deep learning black-box embeddings through probing experiments.

<!-- # SenteCon -->

## Publish using PyPi

To finalize the set-up for publishing to PyPi or Artifactory, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/publishing/#set-up-for-pypi).
For activating the automatic documentation with MkDocs, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/mkdocs/#enabling-the-documentation-on-github).
To enable the code coverage reports, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/codecov/).

## Releasing a new version

- Create an API Token on [Pypi](https://pypi.org/).
- Add the API Token to your projects secrets with the name `PYPI_TOKEN` by visiting [this page](https://github.com/danielmlow/concept-tracker/settings/secrets/actions/new).
- Create a [new release](https://github.com/danielmlow/concept-tracker/releases/new) on Github.
- Create a new tag in the form `*.*.*`.

For more details, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/cicd/#how-to-trigger-a-release).

<!--
# Reproducibility

conda venv concept_tracker python==3.11.5 -->

---

Repository initiated with [fpgmaas/cookiecutter-poetry](https://github.com/fpgmaas/cookiecutter-poetry).
