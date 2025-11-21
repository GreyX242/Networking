# Titre1
## Titre2
### Titre3
#### Titre4
##### Titre5
###### Titre6


**Ecriture *grasse* Gras***  `Je desire faire encore un autre test`  __Test__

~~Les écrite barré~~

==Je usi sle test==  `Je suis le test` 
- [ ] Te suis est test
- [ ] La mangue
- [ ] Safou


[Aller sur ma chaîne Youtube](https://youtube.com/@atte-thm)

```mermaid
graph TD

Biology --> Chemistry

test --> Marcel
```


```mermaid
flowchart LR
	marcel((Je suis marcel))
	marcel --> batela
```


```mermaid
flowchart TB
	id1[Je usi la première case]
	id2([Je suis tres contant])
	id3[[Je recadre les deux coté]]
	id4[(basse)]
	id5>Je suis le test]

id2 --> id1 --> id4
```

# Je suis un peu independant


```mermaid
flowchart LR
	id1{Je suis le test N°2 }
```
# Le suisvant

```mermaid
flowchart LR
	id{{Je suis le test}}
```

# Les relaions

~~~mermaid
flowchart LR

A-->|Je suis lien|B
~~~~
~~~mermaid 
flowchart LR
	LettreA---|Liens|LettreB
~~~


# Lien vace les pointillés

```mermaid
flowchart LR
   A-. text .-> B
   A ==> B
   A --> D
   B ==> C
   B==>D
   A ==> C & A--> F & B --> F & D --> F
```

```mermaid
flowchart TB
	id0[Je suis la case1]
	id1>Je suis la case2]
	id2[Je suis la case3]
	id3(Je suis la case4)
	id4[Je suis la case5]
	id5(Je suis la case6)
	id6[Je suis la case7]
	id7(Je suis la case8)
	id0 ==> id1 & id4 --> id2 -->id4
	id0 ==> id3 & id3 ==Test==>id4
```


```mermaid
flowchart LR
  A ==o B
  id1(Je suis en test1)
  id2(Je suis test2)
  id1 --x id2
```

# Test cool

```mermaid
flowchart LR
    A[Start] --> B{Is it?}
    B -->|Yes| C[OK]
    C --> D[Rethink]
    D --> B
    B ---->|No| E[End]
```

```mermaid
flowchart TD
    A[Start] --> B{Is it?}
    B -->|Yes| C[OK]
    C --> D[Rethink]
    D --> B
    B ---->|No| E[End]
```
## Autre methode

```mermaid
flowchart TD
    A[Start] --> B{Is it?}
    B -- Yes --> C[OK]
    C --> D[Rethink]
    D --> B
    B -- No ----> E[End]
```

# Les blocks

```mermaid
flowchart TB
    c1-->a2
    subgraph one
    a1-->a2
    end
    subgraph two
    b1-->b2
    end
    subgraph three
    c1-->c2
    end
```

# Nommer les blocks

```mermaid
flowchart TB
    c1-->a2
    subgraph ide1 [one]
    a1-->a2
    end
```


# Le Trios

```mermaid
flowchart TB
    c1-->a2
    subgraph one
    a1-->a2
    end
    subgraph two
    b1-->b2
    end
    subgraph three
    c1-->c2
    end
    one --> two
    three --> two
    two --> c2
```



```mermaid
flowchart LR
  subgraph TOP
    direction TB
    subgraph B1
        direction RL
        i1 -->f1
    end
    subgraph B2
        direction BT
        i2 -->f2
    end
  end
  A --> TOP --> B
  B1 --> B2
```

# Correction des limites

```mermaid
flowchart LR
    subgraph subgraph1
        direction TB
        top1[top] --> bottom1[bottom]
    end
    subgraph subgraph2
        direction TB
        top2[top] --> bottom2[bottom]
    end
    %% ^ These subgraphs are identical, except for the links to them:

    %% Link *to* subgraph1: subgraph1 direction is maintained
    outside --> subgraph1
    %% Link *within* subgraph2:
    %% subgraph2 inherits the direction of the top-level graph (LR)
    outside ---> top2
```


# Mon propres test

```mermaid
flowchart LR
    subgraph subgraph1
        direction TB
        top1[top] --> bottom1[bottom]
    end
    subgraph subgraph2
        direction LR
        botton --> bottom2[bottom2]
    end
    subgraph subgraph3
        direction LR
        te(Test) --> bottom2[marce]
    end
    subgraph1 ==> subgraph2
    subgraph2 o==o subgraph3
```
