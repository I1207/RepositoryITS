##### ↑ = Corretta (alt + 24)
##### ↓ = Sbagliata (alt + 25)

## ↑ Indici (Cluestered e Non Clustered)
    Velocizzano le letture

## ↓ Statement SELECT
    Eseguito prima della ORDER BY

        Ordine scrittura    /   Ordine esecuzione
            SELECT          /       FROM
            FROM            /       WHERE
            WHERE           /       GROUP BY
            GROUP BY        /       HAVING
            HAVING          /       SELECT
            ORDER BY        /       ORDER BY

## ↓ Dati strutturati
    Se ogni elemento dell’insieme ha gli stessi attributi e data type

## ↓↑ Table expression
    ~ VIEW 
    ~ TABLE FUNCTION 
   > Anche CTE, Subquery, tabelle derivate (codice dentro le CTE)

   > TF = (vista che ammette dei parametri)

## ↓ Windows function
    Nella SELECT e nella ORDER BY

## ↑ Data type di 1 byte
    256 valori diversi

## ↑ JOIN
    La differenza tra i result set dipendono dalla presenza o meno nelle due tabelle degli stessi valori nei campi della clausola ON della JOIN

## ↓ INSERT INTO TABLE VALUES di più record, se uno di essi va in errore
    Non viene inserito nessun record

## ↓↓↑ Transaction Log
    ~ Il Transaction Log serve per consentire il restore del log nel recovery model FULL
    ~ Il Transaction Log non può essere rimosso
    ~ Il Transaction Log consente la gestione della transazionalità
   > Model FULL = non faccio il backup solo dei dati - SIMPLE - ma anche il backup incrementale ogni giorno, potendo quindi fare il restore FULL / point-in-time

## ↓ select Cliente, Ordine, sum (Valore) as Valore from vendite group by Cliente
    Da errore
   > Nel GROUP BY non c'è Ordine

## ↓ JSON_QUERY
    Recupera il valore di una coppia chiave-valore di un JSON se il valore è di tipo complesso
   > Valori di tipo complesso sono array e dizionari

        SELECT * FROM OPENJSON(@j)
        SELECT @j
            , ISJSON(@j)
            , JSON_VALUE(@j, '$.chiave') AS Chiave
            , JSON_QUERY(@j, '$.array') AS Array
            , PrimoNome = JSON_VALUE(@j, '$.chiave[0].nome')

## ↑ Operatore JOIN
    Opera anche tra tabelle con differente numero di record e di campi

## ↑↑ Affermazioni vere
    ~ Una transazione si può estendere a più batch
    ~ In un batch possono trovarsi più transazioni

## ↑ Differenza campo di tipo char con il valore '821' e di tipo int col valore 821
    Dipende dal valore massimo accettabile definito per il campo char

## Come inserire dei nuovi valori all'interno del mio json?
    insert into schema.json values
    (@j)
    
    OSSERVAZIONE
     Ricordati di inviare anche la dichiarazione della variabile @j perchè non si salva

## Come modificare un valore all'interno di una chiave
    UPDATE Schema.json
    set jsonCol = JSON_MODIFY(jsonCol,'strict$.animali[1].specie','pappagallo')
    where id = 3
    
    OSSERVAZIONE : strict
    Se trova la chiave che hai impostato, si aggiorna,      altrimenti da errore

##  Creazione Procedure

    create procedure schema.sp_insertJson
    
    
    @colore     varchar(20)  not null
    @animaleNome   varchar(20) = null
    @animaleSpecie varchar(20) = null

    as

     ## Struttura IF
    IF (@colore is null)
    BEGIN 
        raiseerror('Il parametro @colore non può essere null')
    END
    ELSE
    ##
    declare @id int = (select max(id) +1
                        from schema.json)

    declare @j nvarchar(max), @jAnimali nvarchar(max)

    
    set @jAnimali  =
       json_modify(json_modify('{}','$.nome,@animaleNome),
                    '$.specie', @animaleSpecie) 
    

    set @j = json_modify('{}','$.id')
    set @j = json_modify(json_modify(@j,'$.colore',@colore),'append$.animali',json_query(@jAnimali))

    --select @j
    insert into schema.json values(@j)
    PRINT 'La registrazione del JSON è andata a buon fine
            Per verifica:
            SELECT top 100 *
            FROM schema.Json
            ORDER BY id desc
END

exec schema.sp_insertJson @colore = 'giallo', @animaleNome = 'pluto'
    
    BEST PRACTICE
    Mettere nel nome un'abbreviazione per indicare cosa sono (stored procedure, viste,...)

    OSSERVAZIONE    
    1. L'identity non è una proprietà della colonna
    2. To avoid automatic escaping, provide newValue by using the JSON_QUERY function.


