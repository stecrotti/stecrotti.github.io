---
layout: page
title: Portfolio
permalink: /portfolio/
nav: true
nav_order: 3
---

Here are some small projects about data and machine learning.


## Protein structure prediction
[Repo](https://github.com/stecrotti/ProteinStructurePrediction)

Keywords: _Computational Biology, Machine Learning, Julia, Protein Folding, Biological Sequences_

A showcase of three different physics-inspired approaches to the problem of predicting the 3D structure of a protein starting from amino-acid sequences. 


## Spurious Correlation Maps
[Repo](https://github.com/stecrotti/SpuriousCorrelationMaps), [Website](https://spuriouscorrelationmaps.streamlit.app/)

Keywords: _Python, Web App Development, Data Processing_

It takes inspiration from the [Spurious Correlation](https://www.tylervigen.com/spurious-correlations) idea by Tyler Vigen, where pairs of timeseries data are checked for unlikely correlations. It is a playful way to remind us how not only that correlation does not imply causation, but that if enough data are compared, some correlation will eventually show up, even if it hides no deep meaning whatsoever.

My idea is to do something similar but with country-based data, which can be visualized by means of colorful maps. For example, it might be that population density happens to correlate well with daily listeners of a certain song? It is far from the maturity stage of the original SpuriousCorrelations website (and it will probably always be, as all it was is an exercise, really): what happens in SpuriousCorrelationMaps is that the user can click a button which will plot a random pair of country-based data, along with the Pearson correlation coefficient. One day I hope to have enough data that it makes sense to plot only the highly correlated ones.

## Gene Expression
[Repo](https://github.com/stecrotti/GeneExpression)

Keywords: _Python, Machine Learning, Classification, Biological data, Logistic Regression, Random Forest_

Training simple ML classifiers to discriminate between two types of tumor based on the expression levels of certain genes measured on patients.

## picmap
[Repo](https://github.com/stecrotti/picmap)

Keywords: _Julia, Python, Geo API, Data Visualization_

This one is divided into two parts:
- Given a database of pictures, each tagged with the GPS location where it was taken, it plots on a world map the density of pictures taken in each area
- A script to add geotags to images searching by location name
