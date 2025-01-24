![Migrate](https://migrate.info/wp-content/uploads/2022/01/Marca_mini.png.webp)

# InvoiCy Framework para Desenvolvedores
Projeto destinado a demonstrar o uso da biblioteca do InvoiCy Framework Android, consumindo Interface de comunicação com a Biblioteca Elgin mini-PDV M8/M10


## Recomendações

Adicionando a biblioteca como dependência:
```Text

	. Copie o arquivo .aar da biblioteca do InvoiCy Framework para a pasta libs
		* Neste repositório, a biblioteca está em:
		InvoicyFrameworkAndroidForDevelopers/app/libs/
	
	. Copie os arquivos .aar da biblioteca Elgin Mini-pdv m8/m10 para a pasta libs
		* Neste repositório, estão sendo utilizados as versões disponíveis até a publicação deste exemplo
		InvoicyFrameworkAndroidForDevelopers/app/libs/
		

	. Clique File - Project Structure

	. Do lado esquerdo, selecione Dependencies

	. Em "All Dependencies", clique no botão com símbolo de "+"

	. Selecione "2 JAR/AAR Dependency"

	. Cole a localização/caminho onde está a biblioteca do InvoiCy Framework

	. Clique OK, para adicionar esta dependência ao projeto

	. Clique Ok, para salvar suas alterações
```
	
Dependências requeridas pela biblioteca InvoiCy Framework

    implementation 'com.google.zxing:core:3.3.0'
    api 'io.jsonwebtoken:jjwt-api:0.11.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
    runtimeOnly('io.jsonwebtoken:jjwt-orgjson:0.11.5') {
        exclude group: 'org.json', module: 'json' //provided by Android natively
    }


Como usar:

	. A classe Minha Venda, é um modelo como deverá consumir os métodos da biblioteca InvoiCy
	Framework Android.
	
	. A classe Parametro, é um modelo como deverá inserir os parâmetros do emitente entre outros
	parâmetros de configuração.


# Aplicação de Exemplo

|UI de Exemplo|
|-------------|
|![Screenshot_20250124_083621 - 02](https://github.com/user-attachments/assets/f3e46d58-6604-46e4-9cb6-0e5d67e7504d)|


## Visualização de impressão utilizando PrintHelper
|Para imprimir, o exemplo utiliza a interface InterfaceElginMinipdv|
|-------------|
|![Screenshot_20250124_083621 - 03](https://github.com/user-attachments/assets/fa457c01-74f6-42ff-89f1-f3e58c8ed802)
|
