<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. the problem</a></li>
<li><a href="#sec-2">2. the method</a></li>
<li><a href="#sec-3">3. discussion</a></li>
</ul>
</div>
</div>


# the problem<a id="sec-1" name="sec-1"></a>

## Some headlines are controversial<a id="sec-1-1" name="sec-1-1"></a>

&#x2026;but some are not. Why?

## some examples<a id="sec-1-2" name="sec-1-2"></a>

> THERE IS NO HIRING BIAS AGAINST WOMAN IN TECH, THEY JUST SUCK AT INTERVIEWS (Breitbart)

. . .

> MONTI PORTA SFIGA (Libero)

. . .

> APRÈ LE NAUFRAGE DE LE PEN, MACRON ARCHIFAVORI (Le Figaro)

## *Controversy*<a id="sec-1-3" name="sec-1-3"></a>

noun, con·tro·ver·sy, ˈkän-trə-ˌvər-sē

> a discussion marked especially by the expression of opposing views (from Merriam-Webster)

# the method<a id="sec-2" name="sec-2"></a>

## a corpus<a id="sec-2-1" name="sec-2-1"></a>

. . . 

facebook pages of newspapers

## annotation<a id="sec-2-2" name="sec-2-2"></a>

. . .

is expensive

. . .

takes time and money

. . .

(I don't have any)

## distant supervision<a id="sec-2-3" name="sec-2-3"></a>

. . .

<img src="./img/react.png" class="img-fluid" alt="Reactions">

. . .

take user's reactions as annotations

## dataset<a id="sec-2-4" name="sec-2-4"></a>

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="left" />

<col  class="right" />

<col  class="right" />

<col  class="right" />
</colgroup>
<thead>
<tr>
<th scope="col" class="left">text</th>
<th scope="col" class="right">LIKE</th>
<th scope="col" class="right">LOVE</th>
<th scope="col" class="right">ANGRY</th>
</tr>
</thead>

<tbody>
<tr>
<td class="left">Le grandi tappe della Guerra fredda</td>
<td class="right">379</td>
<td class="right">1</td>
<td class="right">3</td>
</tr>


<tr>
<td class="left">#Fisco, case ecologiche ed e-bike</td>
<td class="right">393</td>
<td class="right">4</td>
<td class="right">3</td>
</tr>
</tbody>
</table>

## what to do with the counts?<a id="sec-2-5" name="sec-2-5"></a>

. . .

when are they interesting? 

. . .

how much information they encode?

. . .

Time for some **statistics**!

## a recap, before we go on:<a id="sec-2-6" name="sec-2-6"></a>

-   **RQ:** Does the usage of certain words in a text correlate with the reader's emotional reactions? Some texts polarize readers: can we predict this?
-   **Hypo:** the occurrence of certain words (or: the discussion of particular topics) is likely to put the reader in some particular emotional state
-   **Operationalization:** bag-of-word language modeling as a proxy for entropy score computed over user's reactions
-   **Prediction:** a set of word/character n-grams will correlate with entropy scores

## Entropy<a id="sec-2-7" name="sec-2-7"></a>

$$H(X)=\sum_{i}-P(i)log_{2}P(i)$$

## example<a id="sec-2-8" name="sec-2-8"></a>

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="left" />

<col  class="right" />

<col  class="right" />

<col  class="right" />

<col  class="right" />

<col  class="right" />
</colgroup>
<thead>
<tr>
<th scope="col" class="left">&#xa0;</th>
<th scope="col" class="right">LOVE</th>
<th scope="col" class="right">ANGRY</th>
<th scope="col" class="right">HAHA</th>
<th scope="col" class="right">WOW</th>
<th scope="col" class="right">SAD</th>
</tr>
</thead>

<tbody>
<tr>
<td class="left">some text</td>
<td class="right">1</td>
<td class="right">3</td>
<td class="right">1</td>
<td class="right">1</td>
<td class="right">1</td>
</tr>


<tr>
<td class="left">some other text</td>
<td class="right">4</td>
<td class="right">3</td>
<td class="right">11</td>
<td class="right">1</td>
<td class="right">1</td>
</tr>
</tbody>
</table>

## <a id="sec-2-9" name="sec-2-9"></a>

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="right" />

<col  class="right" />

<col  class="right" />

<col  class="right" />

<col  class="right" />

<col  class="right" />
</colgroup>
<thead>
<tr>
<th scope="col" class="right">LOVE</th>
<th scope="col" class="right">ANGRY</th>
<th scope="col" class="right">HAHA</th>
<th scope="col" class="right">WOW</th>
<th scope="col" class="right">SAD</th>
<th scope="col" class="right">Total(N)</th>
</tr>
</thead>

<tbody>
<tr>
<td class="right">1</td>
<td class="right">3</td>
<td class="right">1</td>
<td class="right">1</td>
<td class="right">1</td>
<td class="right">7</td>
</tr>
</tbody>
</table>

Remember:

$$P(i)=count(i)/N$$

$$H(X)=\sum_{i}-P(i)log_{2}P(i)$$

## <a id="sec-2-10" name="sec-2-10"></a>

    reactions <- c(1 ,3 ,1 ,1 ,1)

## Implementing the function<a id="sec-2-11" name="sec-2-11"></a>

    entropy <- function(vector) {
        vprob <- c(vector/sum(vector)) # compute probs
        h <- -sum(vprob*log2(vprob)) # compute H
        return (h)
    }

Remember:

$$H(X)=\sum_{i}-P(i)log_{2}P(i)$$

## outline of the code<a id="sec-2-12" name="sec-2-12"></a>

    reactions <- c(1,3,1,1,1)
    vprob <- c(reactions/sum(reactions))
    print(vprob)
    print(sum(vprob))
    print(-sum(vprob*log2(vprob)))

    [1] 0.1428571 0.4285714 0.1428571 0.1428571 0.1428571
    [1] 1
    [1] 2.128085

## some examples<a id="sec-2-13" name="sec-2-13"></a>

    entropy(c(1,1,1,1,1))

    [1] 2.321928

    entropy(c(100,1,1,1,1))

    [1] 0.3121165

    entropy(c(1))

    [1] 0

## let's sort our dataset<a id="sec-2-14" name="sec-2-14"></a>

## load<a id="sec-2-15" name="sec-2-15"></a>

    df <- read.csv('newsreactions.csv', header=TRUE)
    df = df[-c(1)] # remove the first column, it's an index and R recreates it anyway
    str(df)

    'data.frame':   421 obs. of  8 variables:
     $ message    : Factor w/ 413 levels "'Io sto con la sposa', il Magazine ANSA sul documentario di Gabriele #DelGrande, #FreeDelGrande - http://ow.ly/NiTB30b4J0e",..: 144 109 308 110 229 111 108 373 233 412 ...
     $ description: Factor w/ 291 levels "\n"," ","'Contestazione al contenuto di questa legge totalmente assurda' (ANSA)",..: 6 270 184 24 99 8 24 278 92 105 ...
     $ LIKE       : int  47 11 8 89 39 559 27 2539 135 122 ...
     $ LOVE       : int  0 0 1 10 0 43 0 48 4 7 ...
     $ ANGRY      : int  0 0 0 2 9 15 2 2 48 0 ...
     $ HAHA       : int  0 0 0 1 12 12 0 8 1 0 ...
     $ WOW        : int  1 0 0 2 0 1 0 219 7 0 ...
     $ SAD        : int  0 0 0 0 0 1 3 0 33 0 ...

## compute entropy<a id="sec-2-16" name="sec-2-16"></a>

    df['entropy'] = apply(df[c('LOVE','ANGRY','HAHA','WOW','SAD')], # where to apply
                          1, # (1: rowwise; 2: columnwise)
                          entropy) # function to apply
    
    head(df[c(1,3:9)])

                                                                                                                            message
    1        #Pellegrini: 'Non è ancora il momento per una famiglia, ora penso alle #Olimpiadi del 2020' - http://ow.ly/vfxe30b9ekn
    2                                                             #25aprile, ecco i musei statali aperti - http://ow.ly/ciK930b9bWx
    3                                  La nostalgia delle star: quando il partner è uguale all’ex - VIDEO. http://ow.ly/ttuu30b9bKC
    4 #25aprile, la memoria condivisa lungo i sentieri partigiani e nei luoghi simbolo della #Resistenza - http://ow.ly/m9Si30b9bgO
    5                              Ecco la #manovra, dalla lotta all'evasione ai 3 miliardi per il sisma - http://ow.ly/ihdB30b97zv
    6               #25aprile, partigiana: 'Ricordare per non far tornare quello che ho vissuto' - VIDEO - http://ow.ly/8wtG30b97tq
      LIKE LOVE ANGRY HAHA WOW SAD entropy
    1   47    0     0    0   1   0     NaN
    2   11    0     0    0   0   0     NaN
    3    8    1     0    0   0   0     NaN
    4   89   10     2    1   2   0     NaN
    5   39    0     9   12   0   0     NaN
    6  559   43    15   12   1   1 1.51781

## sort<a id="sec-2-17" name="sec-2-17"></a>

    sorted = df[with(df, # create new (sorted) df
                     order(-entropy)),] # by entropy, increasing (see minus sign)

## Results<a id="sec-2-18" name="sec-2-18"></a>

    head(sorted$message)

    [1] Le grandi tappe della Guerra fredda                                                                                                                                                                                             
    [2] #Corea Nord: #Usa non escludono raid in caso di test nucleare\nhttp://ow.ly/DCnZ30b7wlg                                                                                                                                         
    [3] Ecco cosa è appena successo ad Amici di Maria De Filippi\n\n(e cos'ha detto Morgan prima di lasciare lo studio)                                                                                                                 
    [4] #25Aprile, #Anpi: "Festa di tutti gli italiani". Roma divisa, due celebrazioni. Segui il LIVEBLOG http://ow.ly/Ueqk30b7IC4                                                                                                      
    [5] Il Papa Francesco  in #Egitto non userà auto blindate http://ow.ly/gcAb30b7CIc                                                                                                                                                  
    [6] Dall'Inizio dell'anno nel nostro Paese si sono verificati oltre 1470 casi e il 33% ha avuto complicanze. Nell'elenco anche Germania e Belgio. Nel settembre scorso l'America era stata dichiarata «libera dal morbillo endemico»
    413 Levels: 'Io sto con la sposa', il Magazine ANSA sul documentario di Gabriele #DelGrande, #FreeDelGrande - http://ow.ly/NiTB30b4J0e ...

# discussion<a id="sec-3" name="sec-3"></a>

## <a id="sec-3-1" name="sec-3-1"></a>

1.  work in progress (a better dataset)
2.  from Basic statistics: remember kurtosis?
3.  applications? [TODO ask M. & D. for possible applications in their field]

## THANKS<a id="sec-3-2" name="sec-3-2"></a>
