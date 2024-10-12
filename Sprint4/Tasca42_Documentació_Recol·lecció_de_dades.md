# Exemple de documentació del Procés de Recol·lecció de Dades per al Projecte de Predicció de Despesa Anual de la Botiga de Roba
## 1. Fonts

**Identificació de Fonts:**
- Base de dades interna de l'Entitat bancària, principalment BBDD de tipus realcional sigui en servidors propietaris o al núvol. (i.e. SQL Server, Azure SQL, Oracle ,...).

**Descripció de les Fonts:**
- La base de dades conté informació personal dels clients, els productes financers adquirits i l'experiència en campanyes anteriors. En primer lloc trobem informació que serveix per caracteritzar demograficament al client edat, estat civil, feina. En segon lloc, dades sobre la situació financera actual (mitjana de balanç, tinença de prèstecs i prèstecs fallits en el passat). Finalment trobem dades sobre la seva experiència en campanyes de comunicació (mètode de contacte, número de contactes, resultat de la comunicació,...)
  
## 2. Mètodes de recol·lecció de dades

**Procediments i Eines:**
- Exportació de les dades mitjançant consulta SQL. 

**Freqüència de Recol·lecció:**
- Extracció única i a demanda per tal de fer l'anàlisi dedicat a la campanya.
  
**Scripts de Descàrrega:**

```python
import pandas as pd
import pyodbc

sql_query_file = 'bank_data_query.sql'

server = 'localhost'
database = 'bank_data'
user = 'analytics'
password = 'password'

try:
    conn = pyodbc.connect(
        f'DRIVER={{ODBC Driver 18 for SQL Server}};
        SERVER={server};
        DATABASE={database};
        UID={user};
        PWD={password}'
        )
    cursor = conn.cursor()
    cursor.execute(sql_query_file)
    df = cursor.fetchall()
except:
    print("Error in connection")

print(df.info())


```

## 3. Format i Estructura de les Dades

**Tipus de Dades:**
- Numèrics: `duration`, `campaign`, `pdays`, `previous` , `age`, `balance`, 
- Categòric: `month`,`poutcome`, `y`, `job`,  `marital`, `education`, `default`, `loan`, `housing`,`contact`,`day_week`

**Format d'Emmagatzematge:**
- Dades tabulars emmagatzemades en base de dades.

## 4. Limitacions de les dades

- Les dades són vigents durant un període de temps determinat, si l'estudi o la campanya es dil·laten en el temps caldrà fer una nova extracció.

## 5. Consideracions sobre Dades Sensibles

**Tipus de Dades Sensibles:**
- Informació Personal Identificable (PII): 
- Informació Financera Sensible: `balance`, `default`, `housing`, `loan`
- Dades Comportamentals Sensibles: `day_week`,`contact`,`poutcome`,`month`,`poutcome`, `pdays`,`duration`, `campaign`

**Mesures de Protecció:**
- **Anonimització i Pseudonimització:**
  - S'evitarà el creuament amb bases de dades addicionals que permetin major identificació en base les dades demogràfiques obtingudes (`age`, `marital`, `job`, `education` ).
- **Accés Restringit:**
  - Accés a dades sensibles restringit només a personal autoritzat amb necessitat de conèixer aquestes dades per a fins específics del projecte.
- **Compliment de Regulacions:**
  - Compliment amb la GDRP
