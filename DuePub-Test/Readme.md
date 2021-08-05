To learn catmandu and understand record mode transformations I played around with an workflow for Duepublico2 to Oersi:

You need catmandu and its OAI package:

- [Catmandu](http://librecat.org/Catmandu/#installation)
- its OAI Package `cpanm Catmandu Catmandu::OAI`

To run the workflow: 
`$ catmandu convert OAI --url https://duepublico2.uni-due.de/oer/oai  --metadataPrefix mods --handler mods --fix duepub-catmandu.fix > oersi.json`

Things that I found:

- differences to metafacture morph:
  - While the final metadata structure of the record can be recognised in the Morph. You create the structure and add fitting pieces. Once you set a piece you cannot do anything with it. Catmandu Fix is a reconstruction operation. You have a record and rebuild, move, transform and throw stuff away. Since you remodel a data set you also can remodel already changed fields.
  
  - Catmandu is usind *JSON-Path*, that metafacture is not. While XML files do not have arrays in Catmandu repeatable fields are constructed as arrays.

- It seems that there are no filter functions as in metamorph. Only map a field that conforms to a certain filter. In FIX you have to build an if conditional an then operate. This seems to be more difficult than in metafacture.

- Collectors are no existing concepts in Catmandu. But this makes it complicated to e.g. merge fields.

- Also transformation functions in metafacture seem to be more advanced but also complex. Especially the combination of transformations and filters for one single field are easier and more advanced. In catmandu you only can do one transformation and movement at a time, while in Metamorph you can combine mapping and transformation. This also helps to make the code more readable if that would be possible. In metafacture fix we experimented with `do map ... end`  as a bind for combining mulitple filters and transformation for one field.
