## Código front-end do quiz:

<!DOCTYPE html>
<html lang="pt-BR">

<head>
    <meta charset="UTF-8">
    <title>Quiz Testando Seus Conhecimentos</title>

    <style>
        /* 
        -----------------------------
        ESTILOS DO QUIZ (CSS)
        -----------------------------
        - Define cores, tamanhos, responsividade
        - Deixa a interface mais amigável e organizada
        */

        body {
            font-family: Arial, sans-serif;
            background: #f5f5f5;
            display: flex;
            justify-content: center;
            padding-top: 50px;
        }

        .quiz-container {
            background: white;
            width: 500px;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
        }

        h1 {
            text-align: center;
            margin-bottom: 20px;
        }

        .options label {
            display: block;
            padding: 10px;
            background: #eaeaea;
            margin: 10px 0;
            border-radius: 8px;
            cursor: pointer;
        }

        button {
            width: 100%;
            padding: 12px;
            font-size: 16px;
            background: #4CAF50;
            border: none;
            color: white;
            border-radius: 8px;
            cursor: pointer;
        }

        button:hover {
            background: #45a049;
        }

        #resultado {
            font-size: 18px;
            margin-top: 20px;
            text-align: center;
            font-weight: bold;
        }
    </style>
</head>

<body>

    <!-- 
    ---------------------------------------------------
    INTERFACE DO QUIZ
    ---------------------------------------------------
    - Apenas exibe perguntas e recebe respostas
    - Tudo é controlado por JavaScript
    -->

    <div class="quiz-container">
        <h1>Quiz Testando Seus Conhecimentos</h1>

        <div id="quiz-area"></div>

        <button onclick="responder()">Responder</button>

        <p id="resultado"></p>
    </div>


    <script>
        /* 
        -----------------------------
        SISTEMA DO QUIZ (JAVASCRIPT)
        -----------------------------
        - Controla perguntas, respostas e pontuação
        - Simula o fluxo do back-end
        */

        let perguntas = [
            {
                enunciado: "O que significa HTML?",
                opcoes: [
                    "HyperText Markup Language",
                    "Hyperlinks Management Language",
                    "Home Tool Markup List",
                    "HyperLoop Machine Language"
                ],
                correta: 0
            },
            {
                enunciado: "CSS é usado para estilizar páginas web.",
                opcoes: ["Verdadeiro", "Falso"],
                correta: 0
            }
        ];

        let indice = 0;
        let pontos = 0;

        function carregarPergunta() {
            let p = perguntas[indice];
            let html = `<h3>${p.enunciado}</h3>`;

            p.opcoes.forEach((opc, i) => {
                html += `
                <label>
                    <input type="radio" name="opcao" value="${i}">
                    ${opc}
                </label>`;
            });

            document.getElementById("quiz-area").innerHTML = html;
        }

        function responder() {
            let selecionada = document.querySelector("input[name='opcao']:checked");

            if (!selecionada) {
                alert("Selecione uma opção!");
                return;
            }

            let correta = perguntas[indice].correta;

            if (parseInt(selecionada.value) === correta) {
                pontos += 10;
                document.getElementById("resultado").innerText = "Correto!";
            } else {
                document.getElementById("resultado").innerText = "Errado!";
            }

            indice++;

            if (indice < perguntas.length) {
                setTimeout(() => {
                    document.getElementById("resultado").innerText = "";
                    carregarPergunta();
                }, 1000);
            } else {
                document.getElementById("resultado").innerText = "Quiz finalizado! Pontuação: " + pontos;
                document.getElementById("quiz-area").innerHTML = "";
            }
        }

        carregarPergunta();
    </script>
</body>

</html>

## Código back-end do quiz:

// ===========================================================
//  INTERFACE PONTUAVEL
// ===========================================================
// Serve para definir que qualquer tipo de pergunta
// deve obrigatoriamente implementar a forma de calcular pontuação.
// Cada tipo de pergunta pode ter uma lógica diferente (polimorfismo).
// ===========================================================
interface Pontuavel {
    int calcularPontuacao(boolean correta);
}

// ===========================================================
//  CLASSE ABSTRATA PERGUNTA
// ===========================================================
// - Representa uma pergunta geral (modelo base)
// - Usa ABSTRAÇÃO: não pode ser instanciada
// - Força subclasses a implementar verificarResposta()
// - Mostra ENCAPSULAMENTO: atributos private com getters
// ===========================================================
abstract class Pergunta {

    private String enunciado;
    private String[] opcoes;
    private int correta;

    public Pergunta(String enunciado, String[] opcoes, int correta) {
        this.enunciado = enunciado;
        this.opcoes = opcoes;
        this.correta = correta;
    }

    public String getEnunciado() { return enunciado; }
    public String[] getOpcoes() { return opcoes; }
    public int getCorreta() { return correta; }

    // Método abstrato → deve ser implementado por cada tipo de pergunta
    public abstract boolean verificarResposta(int resposta);
}

// ===========================================================
//  PERGUNTA DE MÚLTIPLA ESCOLHA
// ===========================================================
// - Demonstra HERANÇA: extends Pergunta
// - Demonstra POLIMORFISMO: calcula pontuação diferente
// - Demonstra SOBRESCRITA: sobrescreve verificarResposta()
// ===========================================================
class PerguntaMultiplaEscolha extends Pergunta implements Pontuavel {

    public PerguntaMultiplaEscolha(String enunciado, String[] opcoes, int correta) {
        super(enunciado, opcoes, correta);
    }

    @Override
    public boolean verificarResposta(int resposta) {
        return resposta == getCorreta();
    }

    @Override
    public int calcularPontuacao(boolean correta) {
        return correta ? 10 : 0;
    }
}

// ===========================================================
//  PERGUNTA VERDADEIRO / FALSO
// ===========================================================
// - Constrói automaticamente opções: "Verdadeiro", "Falso"
// - Mostra HERANÇA + POLIMORFISMO igual à anterior
// ===========================================================
class PerguntaVerdadeiroFalso extends Pergunta implements Pontuavel {

    public PerguntaVerdadeiroFalso(String enunciado, boolean correta) {
        super(enunciado, new String[]{"Verdadeiro", "Falso"}, correta ? 0 : 1);
    }

    @Override
    public boolean verificarResposta(int resposta) {
        return resposta == getCorreta();
    }

    @Override
    public int calcularPontuacao(boolean correta) {
        return correta ? 5 : 0; // pontuação diferente só para mostrar polimorfismo
    }
}

// ===========================================================
//  CLASSE JOGADOR
// ===========================================================
// - Simples classe de entidade
// - Demonstra ENCAPSULAMENTO e validação de dados
// ===========================================================
class Jogador {
    private String nome;

    public Jogador(String nome) {
        if (nome.isBlank()) throw new IllegalArgumentException("Nome inválido.");
        this.nome = nome;
    }

    public String getNome() { return nome; }
}

// ===========================================================
//  SISTEMA DE LOGS
// ===========================================================
// - Demonstra SOBRECARGA de métodos
// - Log útil para acompanhar ações internas
// ===========================================================
class SistemaLogs {

    // Método simples
    public void registrarEvento(String msg) {
        System.out.println("[LOG] " + msg);
    }

    // Sobrecarga com NÍVEL
    public void registrarEvento(String msg, int nivel) {
        System.out.println("[LOG - NIVEL " + nivel + "] " + msg);
    }
}

// ===========================================================
//  CLASSE PRINCIPAL QUE RODA O QUIZ
// ===========================================================
// - Demonstra COMPOSIÇÃO (usa Pergunta, Jogador, SistemaLogs)
// - Demonstra ATRIBUTO ESTÁTICO
// - Demonstra POLIMORFISMO (cada pergunta calcula pontos diferente)
// ===========================================================
import java.util.List;
import java.util.Scanner;

class Quiz {

    private static int totalQuizzes = 0; // Atributo compartilhado entre todas instâncias

    private List<Pergunta> perguntas;
    private Jogador jogador;
    private SistemaLogs logs = new SistemaLogs();
    private int pontuacao = 0;

    public Quiz(Jogador jogador, List<Pergunta> perguntas) {
        this.jogador = jogador;
        this.perguntas = perguntas;
        totalQuizzes++;
    }

    public void iniciar() {
        Scanner sc = new Scanner(System.in);

        logs.registrarEvento("Quiz iniciado para " + jogador.getNome(), 1);

        for (Pergunta p : perguntas) {

            System.out.println("\n" + p.getEnunciado());

            String[] ops = p.getOpcoes();
            for (int i = 0; i < ops.length; i++) {
                System.out.println(i + " - " + ops[i]);
            }

            System.out.print("Resposta: ");
            int resp = sc.nextInt();

            boolean correta = p.verificarResposta(resp);

            Pontuavel ptr = (Pontuavel) p; // polimorfismo real
            pontuacao += ptr.calcularPontuacao(correta);

            logs.registrarEvento(correta ? "Resposta correta!" : "Resposta errada!");
        }

        System.out.println("\nPontuação final: " + pontuacao);
        System.out.println("Total de quizzes jogados: " + totalQuizzes);
    }
}

// ===========================================================
//  CLASSE APP — PONTO DE ENTRADA DO PROGRAMA
// ===========================================================
// - Só executa o quiz
// ===========================================================
public class App {
    public static void main(String[] args) {

        Jogador jogador = new Jogador("madu");

        List<Pergunta> perguntas = List.of(
            new PerguntaMultiplaEscolha(
                "O que significa HTML?",
                new String[]{"HyperText Markup Language", "Outra coisa"},
                0
            ),
            new PerguntaVerdadeiroFalso("CSS estiliza páginas web?", true)
        );

        Quiz quiz = new Quiz(jogador, perguntas);
        quiz.iniciar();
    }
}
