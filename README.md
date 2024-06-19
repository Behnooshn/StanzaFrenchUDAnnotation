# StanzaFrenchUDAnnotation
Using Stanza for UD annotation 


!pip install stanza
import stanza

nlp = stanza.Pipeline(lang='fr', processors='tokenize,mwt,pos,lemma,depparse')
doc = nlp('Nous avons atteint la fin du sentier.')


print(*[f'id: {word.id}\tword: {word.text}\thead id: {word.head}\thead: {sent.words[word.head-1].text if word.head > 0 else "root"}\tdeprel: {word.deprel}' for sent in doc.sentences for word in sent.words], sep='\n')

import pandas as pd
import stanza

# Initialize stanza pipeline for French
nlp = stanza.Pipeline(lang='fr', processors='tokenize,mwt,pos,lemma,depparse')

# Load CSV file into a pandas DataFrame
df = pd.read_csv('filtered_en-fr.csv')

# Assuming 'source' is the column containing French sentences
french_sentences = df['source'].tolist()

# Perform dependency parsing on each sentence
parsed_results = []
for sentence in french_sentences:
    # Process each sentence using stanza pipeline
    doc = nlp(sentence)

    # Extract dependency parse information
    parsed_info = [{
        'sentence': sentence,
        'word_id': word.id,
        'word': word.text,
        'head_id': word.head,
        'head': sent.words[word.head - 1].text if word.head > 0 else "root",
        'deprel': word.deprel
    } for sent in doc.sentences for word in sent.words]

    # Append parsed information to results
    parsed_results.extend(parsed_info)

# Create a DataFrame with parsed results
parsed_df = pd.DataFrame(parsed_results)

# Save the parsed results to a new CSV file in the current directory
parsed_df.to_csv('parsed_results.csv', index=False)

# Example of displaying the parsed results
print(parsed_df.head())



# Filter rows where deprel is 'dislocated'
filtered_df = parsed_df[parsed_df['deprel'] == 'dislocated']

# Save the filtered results to a new CSV file in the current directory
filtered_df.to_csv('filtered_dislocated.csv', index=False)

# Example of displaying the filtered results
print(filtered_df.head())
