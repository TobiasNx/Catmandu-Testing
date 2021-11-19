Ich habe ausprobiert wie CATMANDU sich verhält, wenn in verschiedene records mit unterschiedlichen Feldern auftauchen:
I tried how Catmandu is handling records with an field that occurs 1+n times. Some records have multiple some one instance of the field.

Wiederholung eines Feldes mit string-value /Repeated field with string value:
`catmandu convert XML --path "//record" to YAML < repeatable_fields_xml/simpleCreator.xml > repeatable_fields_xml/simpleCreator.yaml`

Mit fix/With fix:
`catmandu convert XML --path "//record" --fix 'repeatable_fields_xml/simple_test.fix' to YAML < repeatable_fields_xml/simpleCreator.xml > repeatable_fields_xml/simpleCreator_withFix.yaml`

Wiederholung eines Object-Feldes / Repeated nested fields:
`catmandu convert XML --path "//record" to YAML < repeatable_fields_xml/nestedCreator.xml > repeatable_fields_xml/nestedCreator.yaml`

Mit fix/with fix:
`catmandu convert XML --path "//record" to YAML < repeatable_fields_xml/nestedCreator.xml --fix 'repeatable_fields_xml/nested_test.fix' > repeatable_fields_xml/nestedCreator_withFix.yaml`