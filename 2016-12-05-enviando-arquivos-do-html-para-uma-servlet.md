# Enviando Arquivos de uma Página HTML para uma Servlet

## Introdução

Na maioria dos sites modernos, temos a necessidade de obter algum tipo de informação dada pelo usuário através de um formulário, na maior parte dos casos, essas captações de dados são bem simples. Podemos simplesmente utilizar um objeto **request** para obtê-los:

```java
request.getParameter("nome_parametro");
```

Esse método nos devolve uma **string** com o valor passado pelo usuário através do formulário ou como parâmetro de uma url inserida manualmente no navegador.

Porém há casos que não são tão simples, e quando temos que receber um arquivo (por exemplo uma foto que será utilizada no perfil deste usuário)? O nosso `getParameter()` não nos ajudará, pois ele devolve uma string.

## Como receber arquivos no servidor

Para esse exemplo iremos utilizar um servidor **Java Web** (como o **Tomcat**), e uma Servlet bem simples para capturar o arquivo e mostrá-lo para usuário, ou então apenas redirecionar para uma nova página informando que o arquivo foi recebido.

Vamos começar pelo nosso html que contém um formulário bem simples.

```html
<!DOCTYPE html>
<html>
<head>
	<title>Upload arquivo</title>
</head>
<body>
	<form action="upload" enctype="multipart/form-data" method="post">
		<p>Enviar Arquivo:
			<input type="file" name="arquivo" />
		</p>
		<p>
			<input type="checkbox" name="inspecionar" value="yes" />
			Gostaria de visualizar o arquivo?
		</p>
		<p>
			<input type="submit" value="Enviar" />
		</p>
	</form>
</body>
</html>
```

> Perceba que temos um atributo especial nessa tag **form**: `enctype="multipart/form-data"`

O atributo **enctype** informa que também será enviado arquivo por esse formulário. O primeiro input é o nosso input file, com ele o usuário irá escolher o arquivo que ele deseja enviar. O segundo input é um checkbox para o servlet saber se deve analisar esse arquivo ou não.

```java
@WebServlet("/upload")
@MultipartConfig(fileSizeThreshold=1000000,maxRequestSize=10000000L)
public class Upload extends HttpServlet {

	private static final long serialVersionUID = 1L;

	public static String getFileName(Part p) {
		String h = p.getHeader("content-disposition");
		String[] sections = h.split("\\s*;\\s*");
		for(String s:sections) {
			if(s.startsWith("filename=")) {
				return s.substring(9).replace("\"", "");
			}
		}
		return null;
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
		Part p = null;
		try {
			p = req.getPart("arquivo");
		} catch(IllegalStateException ise) {
			resp.sendError(HttpServletResponse.SC_REQUEST_ENTITY_TOO_LARGE);
			return;
		}

		if(p==null) {
			resp.sendError(HttpServletResponse.SC_BAD_REQUEST,"Expected file1 part");
			return;
		}

		String mimeType = p.getContentType();
		long fileSize = p.getSize();
		String originalFileName = Upload.getFileName(p);

		if("yes".equals(req.getParameter("inspecionar"))) {
			InputStream is = p.getInputStream();
			ServletOutputStream outputStream = resp.getOutputStream();
			byte[] buffer = new byte[1024];
			int bytes;
			while((bytes=is.read(buffer))!=-1) {
				outputStream.write(buffer, 0, bytes);
			}

			is.close();
			return;
		} else {
			p.delete();
			resp.sendRedirect("envio_ok.html");
		}
	}
}
```

Do lado da nossa Servlet temos a declaração dela e a definição de qual endereço ela irá atender pela anotação `@WebServlet("/upload")`, até o momento nada especial, é apenas uma servlet comum. Para essa servlet capturar arquivos enviados pelo usuário faremos uso da anotação:

```java
@MultipartConfig(fileSizeThreshold=1000000,maxRequestSize=10000000L)
```

Nessa anotação estamos determinando o tamanho máximo do arquivo enviado através do atributo `fileSizeThreshold` com um valor de **10MB** e o tamanho máximo do request.

Para receber o request do cliente devemos sobrescrever  o método `doPost`, já que o nosso form está sendo enviado através do método post. Agora temos acesso ao arquivo que o usuário enviou através do método `req.getPart("arquivo");`. Ele nos devolve um objeto **Part**, cujo podemos obter o **stream** deste arquivo através do `p.getInputStream();`. A partir daí podemos fazer o que bem quisermos.

No exemplo foi apenas realizado a leitura do arquivo e o mesmo foi escrito na resposta de volta para o navegador (caso ele selecione o inspecionar) e caso contrário o usuário é apenas redirecionado para outra página que o informa do sucesso do envio.

Apesar do nosso objeto **Part** possuir um método `getName()`, ele não devolve o nome do arquivo em si, apenas o nome que foi atribuido no input file, no caso **arquivo**. Por isso temos a presença de um método para capturar o nome desse arquivo que foi enviado pelo usuário, e ele se encontra no **header** do nosso **part file**. Nós conseguimos obter ele pela chamada ao método `p.getHeader("content-disposition");`, porém isso retorna um conjunto de informações que não serão analizadas, já que estamos interessados apenas no nome do arquivo, `if(s.startsWith("filename="))`.

Apenas isso já é o suficiente para recebermos o arquivo enviado pelo usuário. A partir desse ponto podemos fazer o que quisermos, salvar esse arquivo no servidor, ou num servidor ftp, persistir ele em um banco de dados, ou que sua aplicação precisar.


## Conclusão

Esse é mais um artigo sobre **Java** dos muitos que ainda vamos ter aqui na **CodeShare**!!! Espero que tenham gostado! Até o próximo artigo!!!

### Escrito por [John Mc.Queide](https://linkedin.com/in/mcqueide/en)