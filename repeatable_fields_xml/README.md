Ich habe versucht wie CATMANDU sich verhält, wenn in verschiedene records mit unterschiedlichen Feldern auftauchen:

`catmandu convert XML --path "//record" to YAML < repeatable_fields_xml/simpleCreator.xml > repeatable_fields_xml/text.yaml`

`catmandu convert XML --path "//record" to YAML < repeatable_fields_xml/nestedCreator.xml > repeatable_fields_xml/text.yaml`