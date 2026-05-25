# Khaleghi_Motlagh_Shahname

## `mdhn-shahnama-tei` — A Multi-Layer Philological Annotation Repository

> *Three verses from the Tahmuras (WD:Q519239) episode as a worked reference implementation*
> of the **TEI XML → IIIF Web Annotation → OWL/RDF (mdhn:)** three-layer architecture.

---

```
نبشتن یکی نه که نزدیک سی          چه رومی چه تازی و چه پارسی
چه سغدی چه چینی و چه پهلوی        نگاریدن آن کجا بشنوی
جهاندار سی سال ازین بیش‌تر         چه‌گونه برون آوریدی هنر
```

*Shahnama · Dāstān-e Tahmuras · Bayts 1–3 of the Shahnama translation passage*

---

## Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Verse Corpus](#verse-corpus)
  - [Bayt 1 — SHN\_NWSH\_V001](#bayt-1--shn_nwsh_v001)
  - [Bayt 2 — SHN\_NWSH\_V002](#bayt-2--shn_nwsh_v002)
  - [Bayt 3 — SHN\_NWSH\_V003](#bayt-3--shn_nwsh_v003)
- [Token Annotation Summary](#token-annotation-summary)
- [Critical Apparatus — Witness Variants](#critical-apparatus--witness-variants)
- [Repository Structure](#repository-structure)
- [Layer 1 · TEI XML](#layer-1--tei-xml)
- [Layer 2 · IIIF Web Annotation](#layer-2--iiif-web-annotation)
- [Layer 3 · OWL / RDF (mdhn:)](#layer-3--owl--rdf-mdhn)
- [SPARQL Queries](#sparql-queries)
- [Ontology Reference](#ontology-reference)
- [Roadmap](#roadmap)
- [How to Contribute](#how-to-contribute)
- [Licence](#licence)

---

## Project Overview

This repository is a **reference implementation** for the annotation methodology
developed in the [MDHN Shahnama Knowledge Graph](https://github.com/MehranDHN) project.
It demonstrates how a single passage of Ferdowsi's *Shahnama* — the three bayts
describing Nowshirwān's commissioning of the translation of the *Khwadāynāmag*
(Book of Kings from Pahlavi) — can be encoded at three interlocking levels of granularity:

| Layer | Standard | Canonical role |
|---|---|---|
| **1** | TEI XML P5 | Token-level annotation, critical apparatus, morphology |
| **2** | IIIF Presentation API 3 + W3C Web Annotation | Image-region linking across manuscript witnesses |
| **3** | OWL 2 / RDF Turtle (`mdhn:` namespace) | Semantic integration, SPARQL, CIDOC-CRM alignment |

The passage is chosen deliberately: it enumerates **six writing systems of the ancient world**
(Rumi, Dari/Tazi, Persian, Sogdian, Chinese, Pahlavi), making it a test-bed for
nisba-adjective annotation, cross-verse correlative chains, verbal-noun disambiguation,
and manuscript orthographic variation — problems that recur throughout all 50,000 verses
of the Shahnama tradition.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    mdhn: OWL Ontology (Layer 3)                 │
│   mdhn:Verse · mdhn:Hemistich · mdhn:Token · mdhn:WitnessReading│
│   teiXmlRef ──────────────────────────────────────────────────► │
│   iiifCanvasRef ──────────────────────────────────────────────► │
└──────────────────────┬──────────────────────┬───────────────────┘
                       │                      │
          ┌────────────▼────────┐   ┌─────────▼──────────────────┐
          │   TEI XML (Layer 1) │   │ IIIF Web Annotation (Layer 2)│
          │ <lg> <l> <w>        │   │ Annotation Page per canvas  │
          │ @ana → #posLib      │   │ body: TextualBody + TEI ref │
          │ <app><rdg wit="…">  │   │ target: canvas#xywh         │
          │ <fs> standoff morph │   │ mdhn:hemistichId cross-ref  │
          │ 50 witness sigla    │   │ seeAlso → TEI document      │
          └─────────────────────┘   └────────────────────────────┘
                       ▲                      ▲
                       └──────────────────────┘
                          seeAlso (IIIF manifest)
```

The **IIIF manifest** links to the TEI document via `seeAlso` with
`format: "application/tei+xml"` and `profile: "https://tei-c.org/ns/1.0"`.
The **OWL graph** holds both `mdhn:teiXmlRef` (URI fragment to `xml:id`) and
`mdhn:iiifCanvasRef` (canvas URI with `#xywh` fragment selector),
making every hemistich and token reachable from SPARQL.

---

## Verse Corpus
### Bayt 1 — SHN\_NWSH\_V001

| | Persian | Transliteration | Translation |
|---|---|---|---|
| **H1** | نبشتن یکی نه که نزدیک سی | *nabeshtan yakī na ke nazdīk sī* | Writing [in] not one [script] but close to thirty |
| **H2** | چه رومی چه تازی و چه پارسی | *che Rūmī che Tāzī o che Pārsī* | Whether Roman, whether Arabic/Dari, and whether Persian |

**Key annotation features**

- `نبشتن` — archaic masdar (verbal noun) used as grammatical subject; `@ana="#NOUN #verbal-noun"`;
  `mdhn:modernEquivalent "نوشتن"`. Deliberate lexical pair with `نگاریدن` in V002.
- `نه ... که` — adversative negative pair: `PART(neg)` + `CONJ(adversative)`.
- `نزدیک سی` — approximative numeral phrase; `نزدیک` here is `ADV(approx)` modifying `NUM`.
- `چه ... چه ... چه` — first three members of a **six-token cross-verse correlative chain**
  that continues into Bayt 2. All six tokens share `lemma="چه"` and `subtype="correlative"`.
- **Critical variant locus**: the second `چه`-complement (`تازی` / `تاری` / `داری` / `دری`)
  is the most contested word in this passage across the manuscript tradition — see apparatus below.

---

### Bayt 2 — SHN\_NWSH\_V002

| | Persian | Transliteration | Translation |
|---|---|---|---|
| **H1** | چه سغدی چه چینی و چه پهلوی | *che Soghdī che Chīnī o che Pahlavī* | Whether Sogdian, whether Chinese, and whether Pahlavi |
| **H2** | نگاریدن آن کجا بشنوی | *negārīdan ān kojā beshnavī* | The writing of it, wherever you may hear [of it] |

**Key annotation features**

- H1 continues the correlative `چه` chain: tokens 4, 5, 6 of six.
  All nisba adjectives (`سغدی`, `چینی`, `پهلوی`) encoded `@ana="#ADJ #nisba"`.
- `نگاریدن` — second masdar-as-noun in the passage; `@ana="#NOUN #verbal-noun"`.
  Bayasanghur substitutes `نگارنده` (agent noun: *the writer*) — a semantically significant shift.
- `کجا` — **disambiguation required**: classical Persian `کجا` is ambiguous between
  locative adverb (*where*) and relative/concessive particle (*of which / wherever*).
  Here it functions as a **relative particle**; encoded `@ana="#PART #relative"` with
  `mdhn:disambigNote` in the OWL graph.
- `بشنوی` — 2nd person singular present subjunctive of `شنیدن`;
  fully decomposed in standoff `<fs>`: prefix `بـ` + stem `شنو` + ending `ی`.

---

### Bayt 3 — SHN\_NWSH\_V003

| | Persian | Transliteration | Translation |
|---|---|---|---|
| **H1** | جهاندار سی سال ازین بیش‌تر | *jahāndār sī sāl azīn bīshtar* | The world-ruler [spent] thirty years — more than this |
| **H2** | چه‌گونه برون آوریدی هنر | *chegūna borun āvarīdī honar* | In what manner did he bring forth [such] skill |

**Key annotation features**

- `جهاندار` — compound noun `جهان + دار` (*world-holder* / *world-ruler*);
  epithet for Nowshirwān. Encoded `@ana="#NOUN #compound"` with `mdhn:referent "Nowshirwan"`.
- `سی سال` — numeral + noun phrase (*thirty years*); `سال` is `NOUN(temporal-unit)`.
- `ازین` — contracted preposition + demonstrative: `از + این`; `@ana="#PREP #clitic"`.
- `بیش‌تر` — comparative adverb (*more*); `@ana="#ADV #comparative"`.
- `چه‌گونه` — interrogative adverb (*in what manner / how*);
  **orthographic variant locus**: some witnesses write `چه گونه` (two words), others `چگونه`.
  Encoded in `<app>` with three `<rdg>` alternatives.
- `برون آوریدی` — **the most morphologically complex token-group in these three bayts**:
  directional prefix `برون` (*outward*) + past stem `آور` + past subjunctive/counterfactual
  ending `یدی` (3sg); expresses *ability or habitual action in the past*.
  Fully decomposed in standoff `<fs>` with `mdhn:mood "counterfactual-past"`.
- `هنر` — **semantic disambiguation required**: in classical Persian, `هنر` means
  *skill, craft, virtue, excellence* — NOT modern *art*. Encoded with `skos:note`.

---

## Token Annotation Summary

| Token ID | Form | Lemma | POS | Subtype | Verse · H |
|---|---|---|---|---|---|
| TK\_N01 | نبشتن | نبشتن | NOUN | verbal-noun | V001·H1 |
| TK\_N02 | یکی | یک | NUM | cardinal | V001·H1 |
| TK\_N03 | نه | نه | PART | neg | V001·H1 |
| TK\_N04 | که | که | CONJ | adversative | V001·H1 |
| TK\_N05 | نزدیک | نزدیک | ADV | approx | V001·H1 |
| TK\_N06 | سی | سی | NUM | cardinal | V001·H1 |
| TK\_N07 | چه | چه | CONJ | correlative | V001·H2 |
| TK\_N08 | رومی | روم | ADJ | nisba | V001·H2 |
| TK\_N09 | چه | چه | CONJ | correlative | V001·H2 |
| TK\_N10 | تازی/تاری/داری/دری | دری/تازی | ADJ | nisba-variant | V001·H2 ⚠️ |
| TK\_N11 | و | و | CONJ | coord | V001·H2 |
| TK\_N12 | چه | چه | CONJ | correlative | V001·H2 |
| TK\_N13 | پارسی | پارس | ADJ | nisba | V001·H2 |
| TK\_N14 | چه | چه | CONJ | correlative | V002·H1 |
| TK\_N15 | سغدی | سغد | ADJ | nisba | V002·H1 |
| TK\_N16 | چه | چه | CONJ | correlative | V002·H1 |
| TK\_N17 | چینی | چین | ADJ | nisba | V002·H1 |
| TK\_N18 | و | و | CONJ | coord | V002·H1 |
| TK\_N19 | چه | چه | CONJ | correlative | V002·H1 |
| TK\_N20 | پهلوی | پهلو | ADJ | nisba | V002·H1 |
| TK\_N21 | نگاریدن | نگاریدن | NOUN | verbal-noun | V002·H2 ⚠️ |
| TK\_N22 | آن | آن | PRON | dem | V002·H2 |
| TK\_N23 | کجا | کجا | PART | relative | V002·H2 ⚠️ |
| TK\_N24 | بشنوی | شنیدن | VERB | subjunctive | V002·H2 |
| TK\_N25 | جهاندار | جهان+دار | NOUN | compound | V003·H1 |
| TK\_N26 | سی | سی | NUM | cardinal | V003·H1 |
| TK\_N27 | سال | سال | NOUN | temporal-unit | V003·H1 |
| TK\_N28 | ازین | از+این | PREP | clitic | V003·H1 |
| TK\_N29 | بیش‌تر | بیش | ADV | comparative | V003·H1 |
| TK\_N30 | چه‌گونه | چه‌گونه | ADV | interrogative | V003·H2 ⚠️ |
| TK\_N31 | برون | برون | PART | directional-prefix | V003·H2 |
| TK\_N32 | آوریدی | آوردن | VERB | counterfactual-past | V003·H2 |
| TK\_N33 | هنر | هنر | NOUN | abstract | V003·H2 ⚠️ |

> ⚠️ = annotation requires explicit disambiguation note or critical apparatus entry

---

## Critical Apparatus — Witness Variants

Five selected witnesses of fifty. Variant loci marked with `*`.

| Siglum | Manuscript | Date | V001·H2 (`تازی` slot) | V002·H2 (verbal noun) | V003·H2 (`چه‌گونه` slot) |
|---|---|---|---|---|---|
| **Fl** | Florence BML Or.20 | c. 1217 | چه **تاری** و چه پارسی | **نگاریدن** آن کجا | چه‌گونه برون آوریدی |
| **Ln** | London BL Add.21103 | c. 1276 | چه **داری** و چه پارسی | نگاریدن آن کجا | چه‌گونه برون آوریدی |
| **Hg** | Houghton Shah Tahmasp | 1524–35 | چه **تازی** و چه پارسی | نگاریدن آن کجا | **چگونه** برون آوریدی |
| **By** | Bayasanghur Golestan | 1430 | چه **تاری** و چه پارسی | **نگارنده** آن کجا | چه‌گونه برون آوریدی |
| **Ca** | Cairo NL Cairo 1 | c. 1300 | چه **دری** و چه پارسی | نگاریدن آن کجا | چه‌گونه برون آوریدی |

**Notes on variants**

(foregrounding Arabic as the language of Islam alongside Persian).
  `تاری`, `داری`, `دری` are orthographic/dialectal variants of *Dari*.
- **`نگارنده` vs `نگاریدن`** (V002·H2, By): Shifts the subject from
  *the act of writing* (verbal noun) to *the writer* (agent noun) —
  a semantically meaningful substitution, not merely orthographic.
- **`چگونه` vs `چه‌گونه`** (V003·H2, Hg): Orthographic merger of a
  two-word interrogative into one; no semantic change but affects tokenisation.

---

## Repository Structure

```
mdhn-shahnama-tei/
│
├── README.md                          ← this file
│
├── tei/
│   ├── SHN_NWSH_V001-V003.xml        ← canonical TEI P5 encoding
│   ├── SHN_NWSH_V001-V003.rng        ← RelaxNG schema (ODD-derived)
│   └── odd/
│       └── mdhn-shahnama.odd          ← ODD customisation (pos, subtype attrs)
│
├── iiif/
│   ├── manifests/
│   │   ├── MS_Florence_f042r.json     ← IIIF Presentation API 3 manifest
│   │   ├── MS_Houghton_f042r.json
│   │   └── MS_Bayasanghur_f042r.json
│   └── annotations/
│       ├── anno-page-V001.json        ← W3C Annotation Page, Bayt 1
│       ├── anno-page-V002.json        ← W3C Annotation Page, Bayt 2
│       └── anno-page-V003.json        ← W3C Annotation Page, Bayt 3
│
├── ontology/
│   ├── mdhn-shahnama.ttl             ← OWL 2 ontology (Turtle)
│   ├── mdhn-shahnama.shacl.ttl       ← SHACL validation shapes
│   └── instances/
│       ├── SHN_NWSH_V001.ttl
│       ├── SHN_NWSH_V002.ttl
│       └── SHN_NWSH_V003.ttl
│
├── sparql/
│   ├── Q01_nisba_adjectives.rq
│   ├── Q02_correlative_chain.rq
│   ├── Q03_verbal_nouns.rq
│   ├── Q04_witness_variants.rq
│   └── Q05_verb_morphology.rq
│
├── scripts/
│   ├── tei_to_rdf.py                 ← rdflib pipeline: TEI → RDF triples
│   ├── validate_tei.sh               ← jing/xmllint validation
│   └── load_graphdb.sh               ← GraphDB repository loader
│
└── docs/
    ├── annotation-guide.md
    ├── pos-tagset.md
    └── witness-sigla.md
```

---
## Layer 1 · TEI XML

**File:** `tei/SHN_NWSH_V001-V003.xml`

The TEI document is the **canonical philological layer**. Every token is a `<w>` element
with `@xml:id`, `@lemma`, and `@ana` pointing into a feature library declared in `<encodingDesc>`.
Critical apparatus is encoded with `<app>` / `<rdg wit="#...">`.
Morphological detail is in standoff `<fs>` elements in `<standOff>`.

### POS encoding strategy

```xml
<!-- Feature library in teiHeader/encodingDesc -->
<fLib xml:id="posLib">
  <f xml:id="NOUN"/>  <f xml:id="VERB"/>  <f xml:id="ADJ"/>
  <f xml:id="NUM"/>   <f xml:id="CONJ"/>  <f xml:id="PART"/>
  <f xml:id="PRON"/>  <f xml:id="ADV"/>   <f xml:id="PREP"/>
</fLib>
<fLib xml:id="subtypeLib">
  <f xml:id="verbal-noun"/>  <f xml:id="nisba"/>       <f xml:id="correlative"/>
  <f xml:id="relative"/>     <f xml:id="subjunctive"/> <f xml:id="counterfactual-past"/>
  <f xml:id="compound"/>     <f xml:id="comparative"/> <f xml:id="interrogative"/>
  <f xml:id="directional-prefix"/>
</fLib>

<!-- Token using @ana (native TEI) -->
<w xml:id="TK_N01" lemma="نبشتن" ana="#NOUN #verbal-noun">نبشتن</w>
```

### Standoff morphology for Bayt 3 verb

```xml
<!-- برون آوریدی — counterfactual past of آوردن with directional برون -->
<fs xml:id="fs_TK_N32" type="morphology" corresp="#TK_N32">
  <f name="pos">       <string>VERB</string></f>
  <f name="lemma">     <string>آوردن</string></f>
  <f name="dirPrefix"> <string>برون</string></f>
  <f name="stem">      <string>آور</string></f>
  <f name="ending">    <string>یدی</string></f>
  <f name="person">    <numeric value="3"/></f>
  <f name="number">    <string>singular</string></f>
  <f name="mood">      <string>counterfactual-past</string></f>
  <f name="tense">     <string>past</string></f>
  <f name="aspect">    <string>habitual</string></f>
</fs>
```

### Critical apparatus for Bayt 3

```xml
<!-- چه‌گونه vs چگونه orthographic variant -->
<l xml:id="SHN_NWSH_V003_H2" type="masra-e-dovvom">
  <app>
    <rdg wit="#MS_Florence #MS_London #MS_Bayasanghur #MS_Cairo">
      <w xml:id="TK_N30_base" lemma="چه‌گونه"
         ana="#ADV #interrogative">چه‌گونه</w>
    </rdg>
    <rdg wit="#MS_Houghton">
      <w xml:id="TK_N30_Hg" lemma="چه‌گونه"
         ana="#ADV #interrogative"
         mdhn:variantNote="orthographic merger: چگونه (one word)">چگونه</w>
    </rdg>
  </app>
  <w xml:id="TK_N31" lemma="برون"   ana="#PART #directional-prefix">برون</w>
  <w xml:id="TK_N32" lemma="آوردن"  ana="#VERB #counterfactual-past"
     mdhn:person="3" mdhn:number="sg" mdhn:mood="counterfactual-past">آوریدی</w>
  <w xml:id="TK_N33" lemma="هنر"    ana="#NOUN #abstract"
     mdhn:semanticNote="skill/virtue/excellence — not modern 'art'">هنر</w>
</l>
```

---

## Layer 2 · IIIF Web Annotation

**Files:** `iiif/annotations/anno-page-V003.json`

Each hemistich maps to one `Annotation` with:
- A `TextualBody` (transcription)
- A `TextualBody` with `purpose: "commenting"` (linguistic notes)
- A `SpecificResource` pointing to the TEI `xml:id` (cross-layer bridge)
- A `FragmentSelector` on the IIIF canvas (`#xywh`)

```json
{
  "@context": "http://www.w3.org/ns/anno.jsonld",
  "id": "https://mdhn.example.org/anno/SHN_NWSH_V003_H2",
  "type": "Annotation",
  "motivation": ["transcribing", "supplementing"],
  "body": [
    {
      "type": "TextualBody",
      "value": "چه‌گونه برون آوریدی هنر",
      "language": "fa",
      "purpose": "transcribing"
    },
    {
      "type": "TextualBody",
      "purpose": "commenting",
      "value": "برون آوریدی: counterfactual-past of آوردن with directional prefix برون (outward). هنر: skill/virtue/excellence in classical Persian, not 'art'. Houghton reads چگونه (one word) vs چه‌گونه in base witnesses.",
      "language": "en"
    },
    {
      "type": "SpecificResource",
      "source": {
        "id": "https://mdhn.example.org/tei/SHN_NWSH.xml#SHN_NWSH_V003_H2",
        "type": "Text",
        "format": "application/tei+xml",
        "profile": "https://tei-c.org/ns/1.0"
      },
      "purpose": "supplementing"
    }
  ],
  "target": {
    "source": "https://iiif.example.org/MS_Florence/canvas/f042r",
    "selector": {
      "type": "FragmentSelector",
      "conformsTo": "http://www.w3.org/TR/media-frags/",
      "value": "xywh=185,816,1760,88"
    }
  },
  "mdhn:verseId":     "SHN_NWSH_V003",
  "mdhn:hemistichId": "SHN_NWSH_V003_H2",
  "mdhn:witness":     "MS_Florence",
  "mdhn:variantTokens": ["TK_N30_base", "TK_N32"]
}
```

### IIIF Manifest `seeAlso` bridge

Each manifest links back to the TEI document:

```json
"seeAlso": [
  {
    "id": "https://mdhn.example.org/tei/SHN_NWSH.xml",
    "type": "Dataset",
    "format": "application/tei+xml",
    "profile": "https://tei-c.org/ns/1.0",
    "label": { "en": ["TEI P5 critical edition — Nowshirvan passage"] }
  },
  {
    "id": "https://mdhn.example.org/ontology/mdhn-shahnama.ttl",
    "type": "Dataset",
    "format": "text/turtle",
    "profile": "http://www.w3.org/2002/07/owl#Ontology",
    "label": { "en": ["MDHN OWL ontology"] }
  }
]
```
---

## Layer 3 · OWL / RDF (mdhn:)

**Files:** `ontology/mdhn-shahnama.ttl`, `ontology/instances/SHN_NWSH_V003.ttl`

### Class hierarchy (relevant subset)

```
crm:E33_Linguistic_Object
    └── mdhn:Verse
          └── mdhn:Hemistich
crm:E90_Symbolic_Object
    └── mdhn:Token
crm:E73_Information_Object
    └── mdhn:WitnessReading
```

### Bayt 3 instance data

```turtle
@prefix mdhn: <http://example.com/mdhn/> .
@prefix crm:  <http://www.cidoc-crm.org/cidoc-crm/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .

mdhn:SHN_NWSH_V003 a mdhn:Verse ;
    crm:P1_is_identified_by "SHN_NWSH_V003" ;
    mdhn:hasHemistich mdhn:SHN_NWSH_V003_H1 ,
                      mdhn:SHN_NWSH_V003_H2 ;
    mdhn:teiXmlRef
      "https://mdhn.example.org/tei/SHN_NWSH.xml#SHN_NWSH_V003"^^xsd:anyURI .

# ── Bayt 3, Hemistich 1 ───────────────────────────────────────────────────────

mdhn:TK_N25 a mdhn:Token ;
    mdhn:form     "جهاندار" ;
    mdhn:lemma    "جهان+دار" ;
    mdhn:pos      "NOUN" ;
    mdhn:subtype  "compound" ;
    mdhn:referent "Nowshirwan" ;
    skos:note     "Compound epithet: جهان (world) + دار (holder/ruler)"@en .

mdhn:TK_N28 a mdhn:Token ;
    mdhn:form     "ازین" ;
    mdhn:lemma    "از+این" ;
    mdhn:pos      "PREP" ;
    mdhn:subtype  "clitic" ;
    skos:note     "Contracted از + این; common Shahnama orthographic fusion"@en .

mdhn:TK_N29 a mdhn:Token ;
    mdhn:form     "بیش‌تر" ;
    mdhn:lemma    "بیش" ;
    mdhn:pos      "ADV" ;
    mdhn:subtype  "comparative" .

# ── Bayt 3, Hemistich 2 ───────────────────────────────────────────────────────

mdhn:TK_N30_base a mdhn:Token ;
    mdhn:form      "چه‌گونه" ;
    mdhn:lemma     "چه‌گونه" ;
    mdhn:pos       "ADV" ;
    mdhn:subtype   "interrogative" ;
    mdhn:attestedBy mdhn:MS_Florence, mdhn:MS_London,
                    mdhn:MS_Bayasanghur, mdhn:MS_Cairo .

mdhn:TK_N30_Hg a mdhn:Token ;
    mdhn:form      "چگونه" ;
    mdhn:lemma     "چه‌گونه" ;
    mdhn:pos       "ADV" ;
    mdhn:subtype   "interrogative" ;
    mdhn:attestedBy mdhn:MS_Houghton ;
    skos:note      "Orthographic merger; no semantic change"@en .

mdhn:TK_N32 a mdhn:Token ;
    mdhn:form             "آوریدی" ;
    mdhn:lemma            "آوردن" ;
    mdhn:pos              "VERB" ;
    mdhn:subtype          "counterfactual-past" ;
    mdhn:directionalPrefix "برون" ;
    mdhn:stem             "آور" ;
    mdhn:ending           "یدی" ;
    mdhn:person           "3"^^xsd:integer ;
    mdhn:number           "singular" ;
    mdhn:mood             "counterfactual-past" ;
    mdhn:tense            "past" ;
    mdhn:aspect           "habitual" .

mdhn:TK_N33 a mdhn:Token ;
    mdhn:form     "هنر" ;
    mdhn:lemma    "هنر" ;
    mdhn:pos      "NOUN" ;
    mdhn:subtype  "abstract" ;
    skos:note     "Classical meaning: skill/virtue/excellence. Not modern 'art'."@en .

# ── Witness readings for V003 ─────────────────────────────────────────────────

mdhn:RDG_V003_H2_Houghton a mdhn:WitnessReading ;
    mdhn:attestedBy mdhn:MS_Houghton ;
    mdhn:hemistich  mdhn:SHN_NWSH_V003_H2 ;
    mdhn:form       "چگونه برون آوریدی هنر" ;
    mdhn:variantToken mdhn:TK_N30_Hg ;
    skos:note "Orthographic variant only: چگونه for چه‌گونه"@en .
```

---

## SPARQL Queries

Stored in `sparql/`. Run against a GraphDB repository loaded with the instance Turtle files.

### Q1 — All nisba adjectives (scripts enumerated)

```sparql
SELECT ?form ?lemma ?verse WHERE {
  ?v mdhn:hasHemistich ?h .
  ?h mdhn:hasToken ?t .
  ?t mdhn:pos "ADJ" ; mdhn:subtype "nisba" ;
     mdhn:form ?form ; mdhn:lemma ?lemma .
  BIND(STRAFTER(STR(?v),"mdhn/") AS ?verse)
}
ORDER BY ?verse
```

### Q2 — The six-token correlative chain

```sparql
SELECT ?verse ?hemistich ?tokenId WHERE {
  ?v mdhn:hasHemistich ?h .
  ?h mdhn:hasToken ?t .
  ?t mdhn:lemma "چه" ; mdhn:subtype "correlative" .
  BIND(STRAFTER(STR(?v),"mdhn/") AS ?verse)
  BIND(STRAFTER(STR(?h),"mdhn/") AS ?hemistich)
  BIND(STRAFTER(STR(?t),"mdhn/") AS ?tokenId)
}
```

### Q3 — Verbal nouns (masdar-as-noun) across the passage

```sparql
SELECT ?form ?lemma ?verse WHERE {
  ?v mdhn:hasHemistich ?h .
  ?h mdhn:hasToken ?t .
  ?t mdhn:pos "NOUN" ; mdhn:subtype "verbal-noun" ;
     mdhn:form ?form ; mdhn:lemma ?lemma .
  BIND(STRAFTER(STR(?v),"mdhn/") AS ?verse)
}
```

### Q4 — Full morphology of Bayt 3 verb

```sparql
SELECT ?form ?lemma ?dirPrefix ?stem ?ending
       ?person ?number ?mood ?tense ?aspect WHERE {
  mdhn:TK_N32
    mdhn:form ?form ; mdhn:lemma ?lemma ;
    mdhn:directionalPrefix ?dirPrefix ;
    mdhn:stem ?stem ; mdhn:ending ?ending ;
    mdhn:person ?person ; mdhn:number ?number ;
    mdhn:mood ?mood ; mdhn:tense ?tense ;
    mdhn:aspect ?aspect .
}
```

### Q5 — All variant tokens with witnesses

```sparql
SELECT ?siglum ?hemistich ?readingForm ?variantToken WHERE {
  ?rdg a mdhn:WitnessReading ;
       mdhn:hemistich ?h ;
       mdhn:form ?readingForm ;
       mdhn:attestedBy ?ms .
  OPTIONAL { ?rdg mdhn:variantToken ?vt .
             BIND(STRAFTER(STR(?vt),"mdhn/") AS ?variantToken) }
  ?ms crm:P1_is_identified_by ?siglum .
  BIND(STRAFTER(STR(?h),"mdhn/") AS ?hemistich)
}
ORDER BY ?hemistich ?siglum
```

---

## Ontology Reference

**Namespace:** `mdhn: <http://example.com/mdhn/>`

### Core classes

| Class | Superclass | Description |
|---|---|---|
| `mdhn:Verse` | `crm:E33_Linguistic_Object` | A single bayt (بیت) |
| `mdhn:Hemistich` | `crm:E33_Linguistic_Object` | A masra (مصراع); half-bayt |
| `mdhn:Token` | `crm:E90_Symbolic_Object` | Orthographic word token |
| `mdhn:WitnessReading` | `crm:E73_Information_Object` | One manuscript's reading |
| `mdhn:Manuscript` | `crm:E22_Human-Made_Object` | Physical manuscript witness |

### Key properties

| Property | Domain | Range | Description |
|---|---|---|---|
| `mdhn:hasHemistich` | `Verse` | `Hemistich` | Verse → its two hemistichs |
| `mdhn:hasToken` | `Hemistich` | `Token` | Hemistich → its tokens |
| `mdhn:hasReading` | `Hemistich` | `WitnessReading` | Hemistich → apparatus readings |
| `mdhn:attestedBy` | `WitnessReading` | `Manuscript` | Reading → witness MS |
| `mdhn:teiXmlRef` | `Verse\|Hemistich\|Token` | `xsd:anyURI` | URI fragment to TEI `xml:id` |
| `mdhn:iiifCanvasRef` | `Hemistich` | `xsd:anyURI` | IIIF canvas + `#xywh` |
| `mdhn:form` | `Token` | `xsd:string` | Surface orthographic form |
| `mdhn:lemma` | `Token` | `xsd:string` | Dictionary/citation form |
| `mdhn:pos` | `Token` | `xsd:string` | Part-of-speech tag |
| `mdhn:subtype` | `Token` | `xsd:string` | Fine-grained annotation subtype |
| `mdhn:mood` | `Token` | `xsd:string` | Verbal mood |
| `mdhn:disambigNote` | `Token` | `rdf:langString` | Explicit disambiguation |

---

## Roadmap

### Phase 1 · Foundation ✅ (this repository)

- [x] Three-bayt pilot corpus — Nowshirvan passage, Bayts 1–3
- [x] TEI P5 encoding with `<app>/<rdg>` apparatus (5 witnesses)
- [x] `@ana` + standoff `<fs>` for all POS and morphological annotation
- [x] W3C Web Annotations for all six hemistichs (Annotation Pages per bayt)
- [x] OWL instance data for all 33 tokens with full morphology
- [x] Five SPARQL queries covering major annotation types
- [x] README as living specification document

### Phase 2 · Validation & Tooling 🔧

- [ ] ODD customisation (`mdhn-shahnama.odd`) with `@pos` and `@subtype` attribute declarations
- [ ] RelaxNG schema derived from ODD (`SHN_NWSH_V001-V003.rng`)
- [ ] SHACL shapes for OWL graph (`mdhn-shahnama.shacl.ttl`)
- [ ] `tei_to_rdf.py` — automated pipeline: TEI `<w>` → `mdhn:Token` triples via `rdflib`
- [ ] `validate_tei.sh` — jing + xmllint CI validation
- [ ] GitHub Actions workflow: validate TEI → generate RDF → load GraphDB on push

### Phase 3 · Corpus Expansion 📖

- [ ] Full Nowshirvan episode (~120 bayts)
- [ ] Prologue (*آغاز کتاب*) — 100 bayts including the opening verse (Bayt 1)
- [ ] Expand witness collation from 5 → 20 manuscripts
- [ ] Integrate Florence BML Or.20 IIIF manifest (Bodleian/BML partnership)
- [ ] Integrate Houghton MS IIIF manifest (Harvard Digital Collections)
- [ ] Cross-folio alignment: same verse across different manuscript canvases

### Phase 4 · Semantic Enrichment 🔗

- [ ] Wikidata reconciliation for named entities (`جهاندار` → Nowshirwān → Q199419)
- [ ] Getty AAT alignment for script/language names (`پهلوی` → AAT 300388270)
- [ ] VIAF alignment for manuscript witness records
- [ ] `mdhn:ZoomedScene` subclass for IIIF Image API region crops of illuminated folios
- [ ] Link to [MDHN Shahnama Knowledge Graph](https://github.com/MehranDHN) main repository
- [ ] MLDCHA aggregator integration via IIIF Collection `seeAlso`

### Phase 5 · Publication & Interoperability 🌐

- [ ] Zenodo DOI for each stable release of the TEI corpus
- [ ] Mirador 3 viewer configuration with annotation display
- [ ] SPARQL endpoint (public GraphDB or Oxigraph) with example queries
- [ ] Export to CoNLL-U format for computational linguistics pipelines
- [ ] Universal Dependencies (UD) tagset alignment for POS tags
- [ ] NIF (NLP Interchange Format) RDF serialisation for NLP interoperability
- [ ] Integration with OpenITI / KITAB corpus infrastructure

---

## How to Contribute

Contributions are welcome in the following areas:

**Philology**
- Additional manuscript readings for the critical apparatus
- Corrections to lemmatisation or POS assignments
- Extended linguistic notes for complex tokens

**Technical**
- Improvements to the ODD customisation
- Additional SPARQL queries
- Python tooling for TEI → RDF conversion

**Manuscripts**
- IIIF manifests for additional witnesses
- Canvas coordinate corrections
- Folio identification and dating

Please open an issue before submitting a pull request for substantive changes.
See `docs/annotation-guide.md` for the full annotation decision log.

---

## Licence

- **Text** (TEI XML corpus, annotations): [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Ontology** (`mdhn:` OWL, SHACL): [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Code** (Python scripts, SPARQL queries): [MIT](https://opensource.org/licenses/MIT)
- **Manuscript images**: images are NOT included in this repository;
  they are referenced via IIIF URIs and remain under the licence of their holding institutions.

---

*Part of the [MDHN — Multi-Layer Digital Heritage Network](https://github.com/MehranDHN) project.*
*Shahnama of Ferdowsi · داستان تهمورث · Nowshirvan episode · Khwadāynāmag translation passage*
