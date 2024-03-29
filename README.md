# Sistema de Dialogo(Unity engine)
Sistema de dialogo utilizando UItext do canvas na engine Unity, através de scripts c#. <br> <br>
Versão 1.0<br><br>
*O que o sistema de dialogo faz?*<br>
Esses scripts retornam um dialogo dentro de um json file, que você pode editar facilmente através de um editor de texto a sua preferencia.
##Arquivos principais

**Você pode importar o projeto completo, que está no arquivo Project.rar**<br>
Existem 2 scripts importante para o projeto(essênciais), são eles:
* DialogControll
* JsonDialogReader <br>

Caso você irá utilizar botões para chamar os dialogos, é importante que você não se esqueça do "ClickEvent", pois o [Onclick](https://docs.unity3d.com/ScriptReference/UI.Button-onClick.html) do Unity suporta apenas 1 parâmetro e a função pede 2 parâmetros.<br><br>

###O que cada script faz ?
**JsonDialogReader:** <br>
```cs
string str;
    JSONNode json;


    string Read()
    {
        string filePath = (Application.persistentDataPath + "/text.json"); 
        StreamReader sr = new StreamReader(filePath);
        string content = sr.ReadToEnd();
        sr.Close();
        return content;
    }


    public string getJsonDialog(string name, int index)
    {
      
        str = Read();
        json = JSON.Parse(str);
        return json[name]["dialogs"][index]["content"];
    }
    
```

A função `Read()` lê e retorna todo o arquivo json, para  mudar o diretório de onde o arquivo se encontra, mude a seguinte linha:
```cs
string filePath = (Application.persistentDataPath + "/text.json"); //  text.json é o nome do seu arquivo onde está os dialogos
```
A função `getJsonDialog()` é a principal função, ela retorna uma string, que no caso é o dialogo, você pode simplesmente chama-la passando dois parâmetros, ela é chamada na classe DialogControll, que necessita do texto que vai ser dialogado no jogo.
```cs
    totalText = jsonDialogReader.getJsonDialog("Leonardo", 0 ); // Chamando o diálogo(array) 0, do personagem Leonardo.
```
Salientando que **JsonDialogReader:** é uma classe não Behaviour, portanto deve ficar fora do ambiente do game.

<br><br>
**DialogControll**: O DialogControll surgiu do video do canal GamesIndie, para ver o video [clique aqui](https://www.youtube.com/watch?v=U572sXp21DU)<br> 
```cs
    public Text DialogText; 
    public string TotalText; //Texto a ser escrito na tela
    public int CurrentIndex; // Palavra atual do texto que será escrito na tela
    public float TextVelocity; //Intervalo de escrita de cada letra
    public float TimeCout;
    public bool InDialog; //Controle de dialogo

    public JsonDialogReader jsonDialogReader = new JsonDialogReader();


    void Start()
    {
    }

    void Update()
    {
        if (!InDialog)
        {
        }
        else
        {
            TimeCout += Time.deltaTime;
            if(TimeCout > TextVelocity)
            {
                if (CurrentIndex < TotalText.Length)
                {
                    DialogText.text += TotalText[CurrentIndex];
                    CurrentIndex++;
                    TimeCout = 0;
                }
                else
                {
                    InDialog = false;
                    TimeCout = 0;
                    CurrentIndex = 0;
                }              
            }
        }
    }

	public void callDialog(string name, int index)
    {
        DialogText.text = "";
        CurrentIndex = 0;
        TimeCout = 0;
        TotalText = jsonDialogReader.getJsonDialog(name, index );
        InDialog = true;
    }
```
Essa é a classe que você colocará no jogo, crie um GameObject e anexe esse script nele, no inspector você pode mudar a velocidade em que cada letra será escrita.<br>
Dentro do `Update()` temos uma estrutura de decisão que verifica se a variavel *inDialog* é verdadeira ou falsa, sempre que for verdadeira o script pegará Text anexado e tranformará no texto que esta na variavel `totalText`. NUNCA DE FORMA ALGUMA(a não ser que você adapte o script) de um set true nessa variavel sem chamar a função ``callDialog()``, pois ela mesma já seta como true, para que o script execute. Bom vamos lá:<br>

##Passo a passo:
**Não esqueça de adicionar a pasta jsonFileReader no seu projeto, pois lá esta o SimpleJSON, que eu utilizei para ler o arquivo json para saber mais [clique aqui](http://wiki.unity3d.com/index.php/SimpleJSON)**

1. Edite o arquivo json com os seus personagens e falas(coloque no diretorio apontado no script. Que e "assets/dialogos.json")
2. Adicione no projeto os scripts aqui apresentados.
3. Crie um Text(canvas), crie também um objeto vazio chamado "DialogControll" e anexe o script "DialogControll" no mesmo. 
4. Linkar o Text que você criou, no script do GameObject "DialogControll".
5. Digite a velocidade de intervalo de cada letra no DialogControll.
6. Após isso para chamar algum dialogo, e que ele seja escrito na tela, você deve chamar: `callDialog(NomeDoPersonagem,indexDoDialogo);` Por exemplo, meu json está assim: 

```json
{
    "Leonardo":{
        "dialogs" : [
            {
            "content" : "Meu nome é Leonardo Bonetti e eu tenho 18 anos, sou estudante de programação :D."
            },
            {
            "content" : "Gosto muito de c#"
            },
            {
            "content" : "Curso análise e desenvolvimento de sistemas :D."
            }
        ]
    },
    
    "João":{
        "dialogs" : [
            {
            "content" : "Sou joão, eu gosto de javascript, e sou desenvolvedor front-end"
            },
            {
            "content" : "Curso sistemas para internet"
            }
	   ] 
    }
}
```
Se eu quiser chamar a primeira fala do Leonardo, ``callDialog("Leonardo",0);`` Se tudo ocorrer bem, o seu textCanvas irá começar uma "animação" como se o texto do dialogo estivesse sendo escrito.<br>

###SE FOR UTILIZAR O BUTTON DO CANVAS
Caso precise utilizar buttons, não utilzie o Onclick() que já está no botão, pois ele suporta apenas um parâmetro<br>
Em cada botão criado, adicione o script *ClickEvent* e edite os atributos que cada botão passará ao chamar o ``callDialog()``, caso haja necessidade mude o script como desejar.<br>

###Perguntas, problemas e contribuição
Qualquer pergunta e ou problema relacionado ao projeto, vá até issues, e nos diga o que aconteceu. <br>
O projeto é open source, livre para estudo e comercialização, ou seja é da comunidade. Você também pode contribuir, seja no código ou na explicação (caso haja algum erro no código ou na explicação) > FORK > COMMIT > PUSH > PULL (para saber como funciona o GitHubt [clique aqui](https://www.youtube.com/watch?v=WVLhm1AMeYE))
