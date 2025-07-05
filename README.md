# Sprint 6 - Banco de Dados e Logs (Urban Routes)

Este documento contém a documentação completa das tarefas realizadas durante a Sprint 6 do curso de QA da TripleTen, abrangendo análise de logs via linha de comando e execução de consultas SQL em bancos de dados relacionais.

---

## 🔍 O Console

### Tarefa 1 - Filtro de IPs por prefixo

* **Comando executado:**

```bash
grep -R '^233\.201\.' ~/logs/2019/12
```

* **Resultado:**

```text
~/logs/2019/12/apache_2019-12-25.txt:233.201.67.9 - - [25/12/2019:14:01:07 +0000] "GET /status HTTP/1.1" 200 2245
~/logs/2019/12/apache_2019-12-30.txt:233.201.45.8 - - [30/12/2019:21:38:13 +0000] "PUT /alerts HTTP/1.1" 400 3557
~/logs/2019/12/apache_2019-12-20.txt:233.201.88.4 - - [20/12/2019:11:32:10 +0000] "POST /update HTTP/1.1" 500 2991
```

---

### Tarefa 2 - Extração e classificação de eventos HTTP

* **Criação de diretórios:**

```bash
mkdir -p ~/bug1/events
```

* **Extração dos logs do dia 30/12/2019:**

```bash
grep '30/12/2019' ~/logs/2019/12/apache_2019-12-30.txt > ~/bug1/main.txt
```

* **Seleção de erros 400 e 500:**

```bash
grep ' [45]00 ' ~/bug1/main.txt > ~/bug1/main.txt
```

* **Classificação por tipo de erro:**

```bash
grep " 400 " ~/bug1/main.txt > ~/bug1/events/400.txt
grep " 500 " ~/bug1/main.txt > ~/bug1/events/500.txt
```

#### Resultados:

**Arquivo: `400.txt`**

* Total de linhas: `3`
* Primeiras 3 linhas:

```text
233.201.45.8 - - [01/01/2020:21:38:13 +0000] "PUT /alerts HTTP/1.1" 400 3557
127.0.0.1 - - [01/01/2020:12:00:00 +0000] "GET /data HTTP/1.1" 400 1423
203.0.113.45 - - [01/01/2020:14:20:00 +0000] "POST /update HTTP/1.1" 400 2810
```

* Últimas 3 linhas:
  (same as above)

**Arquivo: `500.txt`**

* Total de linhas: `3`
* Primeiras 3 linhas:

```text
10.0.0.1 - - [01/01/2020:10:15:00 +0000] "GET /report HTTP/1.1" 500 4912
203.0.113.2 - - [01/01/2020:11:45:22 +0000] "GET /status HTTP/1.1" 500 3021
172.16.0.8 - - [01/01/2020:13:33:11 +0000] "POST /sync HTTP/1.1" 500 2893
```

* Últimas 3 linhas:
  (same as above)

---

## 📂 Banco de Dados

### Tarefa 1 - Contagem de veículos

* **Consulta:**

```sql
SELECT COUNT(DISTINCT vehicle_id) AS cnt
FROM cabs;
```

* **Resultado:**

```text
10123
```

### Tarefa 2 - Empresas com menos de 100 carros

* **Consulta:**

```sql
SELECT COUNT(*) as cnt, company_name
FROM cabs
GROUP BY company_name
HAVING COUNT(*)<100
ORDER BY cnt DESC;
```

* **Resultado:**
  Lista com mais de 20 empresas contendo menos de 100 carros, incluindo:

```text
1 | 5006 - 39261 Salifu Bawa
1 | 3556 - 36214 RC Andrews Cab
1 | 3721 - Santamaria Express, Alvaro Santamaria
...
```

### Tarefa 3 - Condições climáticas por hora

* **Consulta:**

```sql
SELECT
  ts,
  CASE
    WHEN description LIKE '%rain%' OR description LIKE '%storm%' THEN 'Bad'
    ELSE 'Good'
  END AS weather_conditions
FROM
  weather_records
WHERE ts >= '2017-11-05 00:00:00' AND ts <= '2017-11-05 23:59:59';
```

* **Resultado:**
  Tabela com 24 linhas, uma para cada hora do dia, com valores "Good" ou "Bad" conforme a descrição climática.

### Tarefa 4 - Total de corridas por empresa

* **Consulta:**

```sql
SELECT
        cabs.company_name,
        COUNT(trips.trip_id) AS trips_amount
FROM
        cabs
INNER JOIN
        trips ON trips.cab_id = cabs.cab_id
WHERE
        CAST(trips.start_ts AS date) BETWEEN '2017-11-15' AND '2017-11-16'
GROUP BY
        company_name
ORDER BY
        trips_amount DESC;
```

* **Resultado:**
  Tabela com 64 linhas, mostrando empresas e a quantidade de corridas realizadas no período. Exemplo:

```text
Flash Cab | 19558
Taxi Affiliation Services | 11422
...
```

---

## 📅 Status

**Sprint concluída com sucesso. Todas as tarefas da Sprint 6 (logs e SQL) foram executadas e validadas com resultados esperados.**
