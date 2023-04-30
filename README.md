# <img src="https://user-images.githubusercontent.com/78277885/235327726-d51a506a-4bf4-4794-b46f-01702aac0f6d.png" alt="Banco PAN Java Developer" width="200" height="200">

# Projeto Boas Práticas com DynamoDB


### Ferramentas utilizadas

<img src="https://www.educative.io/api/page/5089276808134656/image/download/6007742343938048" alt="Amazon DynamoDB" width="100" height="100">


 <img src="https://th.bing.com/th/id/OIP.5kjrKcjVI35bfepw-j9N5QAAAA?pid=ImgDet&rs=1" alt="Git Bash" width="50" height="50">

### Comandos para execução do projeto:

##### Criar tabela

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
 ```
  ##### Inserir um item
  
  ```
  aws dynamodb put-item \
    --table-name Music \
    --item file://itemmusic.json \
  ```
  ##### Inserir múltiplos itens 
  
  ```
  aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
  ```
  ##### Criar um index global secundário baeado no título do álbum
  
  ```
  aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
   ```
   ##### Criar um index global secundário baseado no nome do artista e no título do álbum
   
   ```
   aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=Artist,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
  ```
  ##### Criar um index global secundário baseado no título da música e no ano
  
  ```
  aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
 ```
 ##### Pesquisar item por artista
 
 ```
 aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"Iron Maiden"}}'
 ```
 ##### Pesquisar item por artista e título da música
 
 ```
 aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values file://keyconditions.json
 ```
 ##### Pesquisa pelo index secundário baseado no título do álbum
 
 ```
 aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Fear of the Dark"}}'
 ```
 ##### Pesquisa pelo index secundário baseado no nome do artista e no título do álbum
 
 ```
 aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Iron Maiden"},":v_title":{"S":"Fear of the Dark"} }'
```
##### Pesquisa pelo index secundário baseado no título da música e no ano

```
aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Wasting Love"},":v_year":{"S":"1992"} }'
 ```

