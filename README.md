# ArquivosDoc - API de Recebimento de Notas Fiscais Eletrônicas (NFe)

A **API REST da ArquivosDoc** permite que sistemas ERP enviem **arquivos XML de NFe** diretamente para a plataforma de **GED (Gerenciamento Eletrônico de Documentos)**. A comunicação é segura, padronizada e compatível com múltiplas linguagens de programação.

> Esta documentação refere-se exclusivamente à **integração de envio**. O processamento posterior (geração de PDF, assinatura digital, indexação e vinculação documental) ocorre internamente na ArquivosDoc após validação do XML.

## 🔗 Endpoint

```
POST https://api.arquivosdoc.com.br/nfe
```

## 🔐 Autenticação

Autenticação via **Bearer Token**.

**Header obrigatório:**
```
Authorization: Bearer SEU_TOKEN
```

> Solicite seu token de acesso junto à equipe técnica.

## 📄 Cabeçalhos (Headers)

| Nome           | Valor                |
|----------------|----------------------|
| Content-Type   | application/xml      |
| Authorization  | Bearer SEU_TOKEN     |

## 📥 Payload da Requisição

O corpo da requisição deve conter **exclusivamente o conteúdo XML da NFe** (modelo 55).

### Exemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<NFe xmlns="http://www.portalfiscal.inf.br/nfe">
  <infNFe versao="4.00" Id="NFe35170630220797000100550010000000021000000023">
    <!-- conteúdo completo da NFe -->
  </infNFe>
</NFe>
```

## 🧪 Exemplos de Integração

### PHP (cURL)
```php
$xml = file_get_contents("nota.xml");

$ch = curl_init("https://api.arquivosdoc.com.br/nfe");
curl_setopt($ch, CURLOPT_POSTFIELDS, $xml);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Content-Type: application/xml",
  "Authorization: Bearer SEU_TOKEN"
]);
$response = curl_exec($ch);
```

### Python (requests)
```python
import requests

headers = {
    "Content-Type": "application/xml",
    "Authorization": "Bearer SEU_TOKEN"
}

with open("nota.xml", "rb") as f:
    xml = f.read()

response = requests.post("https://api.arquivosdoc.com.br/nfe", data=xml, headers=headers)
print(response.text)
```

### Java (HttpURLConnection)
```java
URL url = new URL("https://api.arquivosdoc.com.br/nfe");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setRequestProperty("Content-Type", "application/xml");
conn.setRequestProperty("Authorization", "Bearer SEU_TOKEN");
conn.setDoOutput(true);

byte[] xmlBytes = Files.readAllBytes(Paths.get("nota.xml"));
try (OutputStream os = conn.getOutputStream()) {
  os.write(xmlBytes);
}

System.out.println("HTTP Status: " + conn.getResponseCode());
```

### C# (.NET 5+)
```csharp
using var client = new HttpClient();
client.DefaultRequestHeaders.Add("Authorization", "Bearer SEU_TOKEN");

var xml = await File.ReadAllTextAsync("nota.xml");
var content = new StringContent(xml, Encoding.UTF8, "application/xml");

var response = await client.PostAsync("https://api.arquivosdoc.com.br/nfe", content);
Console.WriteLine(await response.Content.ReadAsStringAsync());
```

### Node.js (axios)
```js
const axios = require("axios");
const fs = require("fs");

const xml = fs.readFileSync("nota.xml", "utf-8");

axios.post("https://api.arquivosdoc.com.br/nfe", xml, {
  headers: {
    "Content-Type": "application/xml",
    "Authorization": "Bearer SEU_TOKEN"
  }
}).then(res => {
  console.log(res.data);
}).catch(err => {
  console.error(err.response.data);
});
```

### Go (net/http)
```go
package main

import (
	"bytes"
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	xmlData, _ := ioutil.ReadFile("nota.xml")
	req, _ := http.NewRequest("POST", "https://api.arquivosdoc.com.br/nfe", bytes.NewBuffer(xmlData))
	req.Header.Set("Content-Type", "application/xml")
	req.Header.Set("Authorization", "Bearer SEU_TOKEN")

	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	body, _ := ioutil.ReadAll(resp.Body)
	fmt.Println(string(body))
}
```

### Ruby (net/http)
```ruby
require 'net/http'
require 'uri'

uri = URI.parse("https://api.arquivosdoc.com.br/nfe")
request = Net::HTTP::Post.new(uri)
request["Content-Type"] = "application/xml"
request["Authorization"] = "Bearer SEU_TOKEN"
request.body = File.read("nota.xml")

response = Net::HTTP.start(uri.hostname, uri.port, use_ssl: true) do |http|
  http.request(request)
end

puts response.body
```

## 📬 Respostas da API

| Código | Significado                          |
|--------|--------------------------------------|
| 200    | XML recebido e validado com sucesso  |
| 400    | Requisição malformada ou inválida    |
| 401    | Token inválido ou ausente            |
| 500    | Erro interno no servidor             |

### Exemplo de Resposta `200 OK`
```json
{
  "status": "sucesso",
  "mensagem": "XML recebido com sucesso",
  "protocolo": "nfe_20250711_00123"
}
```

## 📌 Considerações

- A API **não aceita arquivos ZIP, RAR ou outros formatos**.
- O sistema realiza **validação de schema XSD** internamente.
- Apenas arquivos do **modelo 55 (NFe)** são aceitos nesta versão da API.
- O protocolo retornado pode ser usado para rastreio futuro da nota.

## 📞 Suporte Técnico

Entre em contato com nossa equipe para:

- Solicitar ou renovar token de acesso
- Suporte técnico em homologação
- Reportar comportamentos inesperados

📧 suporte@arquivosdoc.com.br  
🌐 https://www.arquivosdoc.com.br

## ✅ Compatibilidade

![PHP](https://img.shields.io/badge/PHP-7.4+-777BB4?logo=php&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.6+-3776AB?logo=python&logoColor=white)
![Java](https://img.shields.io/badge/Java-8+-007396?logo=java&logoColor=white)
![CSharp](https://img.shields.io/badge/C%23-.NET%205+-239120?logo=c-sharp&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-14+-339933?logo=node.js&logoColor=white)
![Go](https://img.shields.io/badge/Go-1.15+-00ADD8?logo=go&logoColor=white)
![Ruby](https://img.shields.io/badge/Ruby-2.6+-CC342D?logo=ruby&logoColor=white)
![REST](https://img.shields.io/badge/REST%20API-HTTP-blue)