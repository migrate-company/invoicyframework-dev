![Migrate](https://migrate.info/wp-content/uploads/2022/01/Marca_mini.png.webp)

# Invoicy Framework Android, para desenvolvedores
Este Projeto utiliza a IDE Android Studio, para demonstrar o uso da biblioteca do InvoiCy Framework Android.


# Sobre o projeto

	. A classe Venda, é um modelo como deverá consumir os métodos da biblioteca InvoiCy
	Framework Android.
	
	. A classe Parametro, é um modelo como deverá inserir os parâmetros do emitente entre outros
	parâmetros de configuração.

Neste exemplo também há implementação de **"modelos"** para as interfaces de log's e impressão, onde para esta última, foi utilizado recursos oferecidos pela classe PrintHelper.

Recomendamos que dedique atenção para compreender o uso dos modelos prospostos, em especial no arquivo Venda.java.

São elas:

1. Uso da biblioteca aar na pasta libs, atraves da classe InvoiCyFramework em Venda.java;
2. Classe LogGer, que implementa o uso da interface LogIFW.

   Vale lembrar que nenhum log será armazenado diretamente pelo InvoiCy Framework, sendo requisito a implementação desta interface e o armazenamento destas informações de acordo com o seu projeto.

   Como sugestão, poderá ser utilizado o SQLite como recurso para persistir estas informações pelo prazo desejado, controlando a data em que são gravados.

3. Classe Impressora, que implementa a interface InterfaceImpressora.

   ### Observação:

   A forma como este projeto trata a impressão, é apenas um modelo para conhecimento.

   O InvoiCyFramework possibilita imprimir em:

   1.	Dispositivos que possuêm impressora integrada
   2.	Impressora ethernet, informando o endereço IP
   3.	Ou através do modelo exemplificado neste projeto:

		Utilizando os recursos oferecidos pela classe PrintHelper.

4. No arquivo Parametro.java, encontrará um modelo para informações agrupadas referente aos dados do parceiro.

	Neste arquivo observe o método LeituraDeConfiguracao da classe Parametro;

	Este método contém dados como:

   	. emitente.CNPJ
   
	. emitente.chaveAcesso
   
   	. emitente.chaveParceiro
   
   	. emitente.mod
   
   	. emitente.serie
   
   	. emitente.nNF entre outros;

	Este é um modelo estruturado, onde deverá inserir suas informações de parceiro antes de iniciar testes.


# Android Studio

Orientações para uso.

## Adicionando a biblioteca como dependência:

1. Copie o arquivo .aar da biblioteca do InvoiCy Framework para a pasta libs, ou um local de sua preferência.

	Dica: Lembre-se de copiar a localização ou caminho deste arquivo.

2. Clique File - Project Structure

	. Do lado esquerdo, selecione Dependencies

	. Em "All Dependencies", clique no botão com símbolo de "+"

	. Selecione "2 JAR/AAR Dependency"

	. Cole a localização/caminho onde está a biblioteca do InvoiCy Framework

	. Clique OK, para adicionar esta dependência ao projeto

	. Clique Ok, para salvar suas alterações


## Dependências utilizadas neste projeto

```java
dependencies {

    implementation 'androidx.appcompat:appcompat:1.4.2'
    implementation 'com.google.android.material:material:1.6.1'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'])
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'

    // dependências requeridas pela biblioteca InvoiCy Framework
    implementation 'com.google.zxing:core:3.5.0'
    api 'io.jsonwebtoken:jjwt-api:0.11.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
    runtimeOnly('io.jsonwebtoken:jjwt-orgjson:0.11.5') {
        exclude group: 'org.json', module: 'json' //provided by Android natively
    }
}
```

# Tela de Apresentação, do aplicativo de exemplo:


|1. UI Principal|2. UI Principal: Usando a classe PrintHelper|
|-----------------------------------------------------------------------|-----------------------------------------------------------------------|
|![Screenshot_20250122_210805](https://github.com/user-attachments/assets/dd7e9361-06c9-4146-be57-51e504f46d1d)|![Screenshot_20250122_211037](https://github.com/user-attachments/assets/b5271eb3-751b-4026-83e1-241a9a732407)|

