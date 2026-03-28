# RAG Pipeline — Assistant Municipal de Triffouillis sur Loire

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![Docling](https://img.shields.io/badge/Docling-IBM-orange)
![LangChain](https://img.shields.io/badge/LangChain-Chunking-green)
![ChromaDB](https://img.shields.io/badge/ChromaDB-Vector_Store-purple)
![Mistral](https://img.shields.io/badge/Mistral_AI-LLM-red)
![License](https://img.shields.io/badge/License-MIT-yellow)

## Description

Pipeline **Retrieval-Augmented Generation (RAG)** complet, construit de zéro, permettant d'interroger en langage naturel un ensemble de documents municipaux (PDF, DOCX, images, CSV) de la commune fictive de **Triffouillis sur Loire**.

Le système extrait, structure, vectorise et indexe les documents pour offrir un chatbot capable de répondre précisément aux questions des citoyens en citant ses sources.



---

## Architecture du Pipeline
```
Documents bruts (PDF, DOCX, PNG, CSV...) 
                │ 
                ▼ 
            ① EXTRACTION ──────── Docling (IBM) 
                                        │ 
                        Conversion multi-format → Markdown 
                                        ▼ 
            ② CHUNKING ─────────── LangChain 
                                        │
                    RecursiveCharacterTextSplitter 
                                        │ 
                        (1000 chars, overlap 200) 
                                        ▼ 
            ③ EMBEDDING ─────────── Sentence-Transformers 
                                        │
                    paraphrase-multilingual-MiniLM-L12-v2 
                                        │ 
                    (384 dimensions, multilingue) 
                                        ▼ 
            ④ STOCKAGE ──────────── ChromaDB 
                                        │ 
                    Métrique cosinus (HNSW) 
                                        ▼ 
            ⑤ GÉNÉRATION ────────── Mistral Small (API REST) 
                                        │ 
                Temperature 0.3, grounded prompting 
                                        ▼ 
                        🤖 Réponse sourcée et fiable
```


---

## Résultats Clés

| Métrique | Valeur |
|----------|--------|
| Documents traités | 28 fichiers (PDF, DOCX, PNG, CSV) |
| Taux de conversion Docling | 100% (0 erreur) |
| Chunks générés | 195 |
| Taille moyenne des chunks | 664 caractères |
| Dimension des embeddings | 384 |
| Meilleure distance obtenue | 0.1867 (pertinence 81%) |

### Comparaison des modèles d'embedding

| Modèle | Langue | Similarité (même thème FR) | Similarité (thèmes différents FR) | Discrimination |
|--------|--------|---------------------------|-----------------------------------|----------------|
| all-MiniLM-L6-v2 | Anglais | 0.37 | 0.39 | ❌ Quasi nulle |
| **multilingual-MiniLM-L12-v2** | **Multilingue** | **0.81** | **0.30** | **✅ Excellente** |

> **Conclusion** : Le modèle multilingue offre une discrimination sémantique 10x supérieure sur des documents en français.

---

## Stack Technique

| Composant | Outil | Rôle |
|-----------|-------|------|
| Extraction documentaire | [Docling](https://github.com/docling-project/docling) (IBM) | Conversion PDF/DOCX/images → Markdown |
| Chunking | [LangChain](https://www.langchain.com/) | Découpage intelligent avec overlap |
| Embedding | [Sentence-Transformers](https://www.sbert.net/) | Vectorisation multilingue |
| Base vectorielle | [ChromaDB](https://www.trychroma.com/) | Stockage et recherche sémantique |
| LLM | [Mistral AI](https://mistral.ai/) (Small) | Génération de réponses |
| Environnement | Google Colab | Notebook Python |

---

## 📁 Structure du Projet
```
📦 rag-assistant-municipal/ 
├── 📓 exercice_RAG.ipynb # Notebook principal (pipeline complet) 
├── 📄 README.md # Ce fichier 
├── 📄 .gitignore # Fichiers à exclure 
├── 📄 requirements.txt # Dépendances Python 
└── 📂 documents_markdown/ # (Généré) Documents convertis par Docling 
    ├── 📂 budget/ 
    ├── 📂 communication/ 
    ├── 📂 demandes citoyennes/ 
    ├── 📂 evenements/ 
    ├── 📂 intances/ 
    └── 📂 projets/
```


---

## 🚀 Installation et Utilisation

### Prérequis

- Python 3.10+
- Clé API Mistral AI (gratuite sur [console.mistral.ai](https://console.mistral.ai/api-keys))

### Option 1 : Google Colab (recommandé)

1. Ouvrir `exercice_RAG.ipynb` dans Google Colab
2. Ajouter votre clé Mistral dans les secrets Colab (🔑 → `MISTRAL_API_KEY`)
3. Exécuter les cellules dans l'ordre

### Option 2 : Exécution locale

```bash
git clone https://github.com/VOTRE_USERNAME/rag-assistant-municipal.git
cd rag-assistant-municipal
pip install -r requirements.txt
jupyter notebook exercice_RAG.ipynb
```

## Leçons Apprises

    Le choix du modèle d'embedding est critique — Un modèle anglophone sur des documents français produit des résultats médiocres, même si le modèle est populaire.

    La métrique de distance compte — Passer de L2 (euclidienne) à cosinus a normalisé les scores et amélioré l'interprétabilité.

    Le prompt engineering fait la différence — Un prompt système bien cadré (rester fidèle aux sources, citer les documents, avouer l'ignorance) élimine la majorité des hallucinations.

    L'overlap dans le chunking est essentiel — Sans chevauchement, les idées coupées aux frontières des chunks sont perdues.

    L'API REST directe est plus robuste — Contourner les SDK instables avec requests garantit la compatibilité.

## Améliorations Possibles

    Utiliser un modèle d'embedding plus puissant (Gemini Embedding, CamemBERT)
    Implémenter un re-ranking avec un cross-encoder
    Ajouter un chunking sémantique basé sur les en-têtes Markdown
    Persister ChromaDB sur disque pour un déploiement en production
    Ajouter une interface web avec Streamlit ou Gradio
    Intégrer la gestion de l'historique conversationnel (mémoire)


## 📜 Licence

Ce projet est distribué sous licence MIT. Voir le fichier LICENSE pour plus de détails.

Auteur : Behram Ko. 
Position: Data Engineer Full Stack


## 🙏 Remerciements

    
    IBM / Docling pour l'outil d'extraction
    Mistral AI pour le modèle de langage
    La commune fictive de Triffouillis sur Loire et sa maire Madame Pétillante Rigolade 😄