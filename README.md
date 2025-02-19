# DCPL JSON schema

[![Test](https://github.com/gsileno/DCPLschema/actions/workflows/test.yml/badge.svg)](.github/workflows/test.yml)

DCPL (pronounced "disciple", standing for _duty, claim, power, claim and liability_, or _digital contracts programming language_) is a domain specific language that serves as an information model for specififying norms. As discussions on several aspects of normative concepts and normative systems are still open in the literature, DCPL (at first named DPCL) attempts to remain as much as neutral with respect to the actual semantics, yet aims to provide a minimal common ground to encode normative computational artefacts.

A fast comparison:

- DPCL like ODRL (https://www.w3.org/TR/odrl-model/) aims to provive primarily an informational model, and is JSON-centred, but DPCL include power categories, and focuses on normative mechanisms (it does not include inherently concepts as assets);
- DPCL like FLINT/eFLINT (https://gitlab.com/eflint) takes as primitives frames constructed from Hohfeld's framework of normative concepts, but DPCL strictly separates conditional from normative aspects, and allow specifying a wider array of normative concepts;
- DPCL like Logical English (https://demo.logicalcontracts.com/) takes as primitives transformational and reactive rules to deal with conditional aspects, but DPCL includes explicit normative concepts.

This repository contains a JSON schema validating a DPCL program encoded in a json file.

## Files

- `DPCLschema.json` contains the current version of the information model of DPCL as a JSON schema.
- `DPCLexamples.json` contains examples of code that are validated by the schema
- `DPCLtest.py` is a simple script based on `jsonschema` used to validate both the schema definition and DPCL code in the file `DPCLexamples.json` against the schema.

## Dependencies

```
pip install jsonschema
```

## References

Sileno, G., van Binsbergen, T., Pascucci, M., van Engers, T.,
_DPCL: a Language Template for Normative Specifications_,
Workshop on Programming Languages and the Law (ProLaLa 2022), co-located with POPL 2022
https://arxiv.org/abs/2201.04477
