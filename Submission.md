# The grand challenge of data citation: a critical look at the Making Data Count Data Citation Corpus

Roderic D. M. Page
School of Biodiversity, One Health and Veterinary Medicine College of Medical, Veterinary and Life Sciences University of Glasgow
Glasgow G12 8QQ, UK
https://orcid.org/0000-0002-7101-9767

## Data citation

Bibliographic citation - where one publication cites another - is standard practice in academia. Citations between publications generate a graph or network of citations, the structure of which can provide insights into patterns of research. This graph also enables us to track the provenance of statements and to compute metrics to assess the impact of an individual article, its authors, and the journal where it was published. The value of the citation graph is reflected by it being - until recently - only accessible as a commercial product provided by a small number of vendors such as Elsevier and Clavariate. The Open Citation Corpus now provides free access to the bulk of the citation graph (Peroni and Shotton, 2020).

Yet bibliographic citation only captures relationships between publications. Much of modern science, especially in molecular biology, is data-driven. Researchers use (and reuse) data, most notably biomolecular sequences (e.g., DNA and protein). Early efforts to track citations of sequence data discovered that publications may use sequences without citing the original publication of those sequences (Page 2008). Without a mechanism to keep track of data citation, we lose the ability to track the provenance of data, and give credit to those who generated that data.

Recently, the [Make Data Count initiative](https://makedatacount.org) has released the Data Citation Corpus (DataCite & Make Data Count, 2024a), providing millions of citation links between publications and datasets. While this development has the potential to greatly enhance our ability to document data use and reuse, a critical look at the corpus reveals numerous errors, including citations of fictitious data, and a lack of clarity over what constitutes “citation” (Page, 2024a, 2024b). Here I review the corpus and suggest how it might be improved.

## The corpus and its problems

Version 1 of the corpus was released in early 2024 and contained 10 million data citations (DataCite & Make Data Count, 2024a). However, this release contained millions of duplicates and has been superseded by version 2 (DataCite & Make Data Count, 2024b) containing 5.2 million citations. The analysis presented here is based on version 2 of the corpus.

The citations in the corpus comprise links between a publication  Digital Object identifier (DOI) and an identifier for a data item, which may be a dataset, or in the case of molecular data the accession number for an individual item such as a nucleotide sequence. The corpus combines information from two sources, DataCite (1.2 million citations), and the Chan Zuckerberg Initiative (CZI) (4 million citations). The citations provided by the CZI were obtained by data mining the scientific literature using a language model derived from BERT (Devlin et al., 2019), but the full details of the method used have not been published, nor is the code or model available.

I downloaded version 2 from Zenodo. The data is in JavaScript Object Notation (JSON) format, which I then loaded into an Apache CouchDB database. CouchDB stores JSON documents natively and provides a “map-reduce” framework to summarise those documents.

[Fig 1] The top twenty data repositories ranked by number of citations.

The biggest repository in the corpus is the European Nucleotide Archive (a member of the International Nucleotide Sequence Database Collaboration or INSDC, Karsch-Mizrachi et al. 2012) (Fig. 1). Indeed, most of the repositories in Figure 1 are biomolecular or genomic. Note that there are some obvious data quality issues: Figshare appears twice, as “Figshare” and as “figshare”. Combining those two entries still underestimates the role FigShare plays as the repository “Taylor & Francis” is a branded instance of Figshare: [https://tandf.figshare.com](https://tandf.figshare.com).

[Fig 2] The frequency distribution of the number of citations each data item has received.

Counting the number of data citations for each item shows that most items are cited only once (Fig 2). There are notable exceptions, such as **LY294002** with 9983 citations and **A549** with 5883 citations. The corpus treats LY294002 and A549 as accession numbers for molecular sequences, but LY294002 is a chemical compound that acts as an inhibitor, and A549 is cell type. Hence it’s likely that the most highly-cited data records are not data items, but instead are false matches to other biological entities. 

Closer examination shows that false matches plague the corpus, for example (with links to annotated sources):

- A museum specimen **CR00240699** is mistakenly interpreted as a GenBank accession number, see [https://hyp.is/CGTJcM_kEe674TfyvGLC0A/zookeys.pensoft.net/article/21580/download/pdf/287887](https://hyp.is/CGTJcM_kEe674TfyvGLC0A/zookeys.pensoft.net/article/21580/download/pdf/287887)
- A grant number **Y21026** is mistakenly interpreted as a GenBank accession number, see [https://hyp.is/HpVXhs9PEe6D2UMxrIdqJw/bmjopen.bmj.com/content/12/9/e054887](https://hyp.is/HpVXhs9PEe6D2UMxrIdqJw/bmjopen.bmj.com/content/12/9/e054887)

To gauge the extent of these errors I analysed two datasets in more detail. The Protein Data Bank (PDB) is the third most cited resource in version 2 of the corpus with 403,412 individual citations (Fig. 2). We can check whether these are potentially valid by comparing these citations to a list of identifiers obtained from the PDB. This enables us to screen out obviously incorrect citations (such as the “24hr” being treated as a PDB identifier, see Page, 2024a), but still leaves open the possibility that a four-character string may spuriously match a PDB identifier. Of the original PDB citations 14% were not valid PDB identifiers, leaving 86% which at least correspond to a possible PDB record.

GenBank has some 3.7 billion sequences (Sayers et al., 2024) so this approach of comparing citations to a list of identifiers does not readily scale to nucleotide sequences. Of a random sample of 1000 cited nucleotide sequences I was able to find only 51% in the source databases. Extrapolating to the 2.2 million such citations in the corpus it is likely that 1 milliun of these citations are fictitious.

## What does the corpus measure?

That most data items are cited only once (Fig. 2) raises the question as to why just once. Citations to data in the “Taylor & Francis” repository offer some clues. The vast majority of data in this repository is published by Informa UK Ltd which is the parent company of Taylor & Francis. If you visit an article in an Informa journal, such as “Enhanced Selectivity of Ultraviolet-Visible Absorption Spectroscopy with Trilinear Decomposition on Spectral pH Measurements for the Interference-Free Determination of Rutin and Isorhamnetin in Chinese Herbal Medicine” (Zhou et al., 2021) the supplementary information is stored in Figshare [https://doi.org/10.6084/m9.figshare.14103264.v1](https://doi.org/10.6084/m9.figshare.14103264.v1). These links between the publication and the supplementary information are treated as “citations” in the corpus. Yet, this is not a citation in the sense of reuse of published data, rather it records a link between two publication events, the publication and its accompanying data. Metrics based on these “citations” do not measure data reuse, but rather data preservation.


## Next steps

The title of this article "The Grand challenge..." is a nod to the [Elsevier Grand Challenge: Knowledge Enhancement in the Life Sciences](https://web.archive.org/web/20081024064958/http://www.elseviergrandchallenge.com/description.html). The work reported in Page (2008) was my entry in that challenge, and the late David Shotton was one of the judges. Given the significant issues with the Data Citation Corpus, perhaps the time is ripe for a similar competition to develop better methods for accurately tracking citations of data. 


## References

DataCite & Make Data Count (2024a). Data Citation Corpus Data File (Version v1.1) [Dataset]. DataCite. https://doi.org/10.5281/zenodo.11216814
DataCite & Make Data Count. (2024b). Data Citation Corpus Data File (Version v2.0) [Dataset]. DataCite. https://doi.org/10.5281/ZENODO.13376773
Devlin, J., Chang, M.-W., Lee, K., & Toutanova, K. (2019). BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (arXiv:1810.04805). arXiv. https://doi.org/10.48550/arXiv.1810.04805
Karsch-Mizrachi, I., Nakamura, Y., & Cochrane, G. (2012). The International Nucleotide Sequence Database Collaboration. Nucleic Acids Research, 40(Database issue), D33–D37. https://doi.org/10.1093/nar/gkr1006
Page, R. (2024a, February 20). Problems with the DataCite Data Citation Corpus. iPhylo. https://doi.org/10.59350/t80g1-xys37
Page, R. (2024b, October 8). The Data Citation Corpus revisited. iPhylo. https://doi.org/10.59350/wvwva-v7125
Page, R. D. M. (2010). Enhanced display of scientific articles using extended metadata. Web Semantics: Science, Services and Agents on the World Wide Web, 8(2–3), 190–195. https://doi.org/10.1016/j.websem.2010.03.004
Peroni, S., & Shotton, D. (2020). OpenCitations, an infrastructure organization for open scholarship. Quantitative Science Studies, 1(1), 428–444. https://doi.org/10.1162/qss_a_00023
Sayers, E. W., Cavanaugh, M., Clark, K., Pruitt, K. D., Sherry, S. T., Yankie, L., & Karsch-Mizrachi, I. (2024). GenBank 2024 Update. Nucleic Acids Research, 52(D1), D134–D137. https://doi.org/10.1093/nar/gkad903
Zhou, P.-R., Tang, Z.-F., Wei, K.-S., Wan, Y., Gao, Y.-M., Liang, Y.-M., Yan, X.-F., Bin, J., & Kang, C. (2021). Enhanced Selectivity of Ultraviolet-Visible Absorption Spectroscopy with Trilinear Decomposition on Spectral pH Measurements for the Interference-Free Determination of Rutin and Isorhamnetin in Chinese Herbal Medicine. Analytical Letters, 54(17), 2750–2768. https://doi.org/10.1080/00032719.2021.1888966


