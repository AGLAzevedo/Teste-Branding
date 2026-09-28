# Configurar o acompanhamento em tempo real (Firebase)

Sem esta configuração, o teste funciona normalmente, mas cada aparelho guarda
seu próprio ranking isolado, e o painel do professor não recebe dados ao vivo.
Com ela (gratuita, leva ~5 minutos), o painel passa a mostrar as respostas de
todos os alunos, em qualquer aparelho, em tempo real.

## 1. Criar o projeto (gratuito)

1. Acesse **https://console.firebase.google.com** e faça login com uma conta Google.
2. Clique em **"Adicionar projeto"**, dê um nome (ex.: `turma-psicologia`) e conclua a criação.
3. Dentro do projeto, clique no ícone **`</>`** ("Adicionar app da Web").
4. Dê um apelido ao app (ex.: `teste-branding`) e clique em **"Registrar app"**.
5. O Firebase vai mostrar um bloco `firebaseConfig = { ... }`. Copie esses 6 valores
   (`apiKey`, `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, `appId`).

## 2. Criar o banco de dados (Firestore)

1. No menu à esquerda, vá em **Build → Firestore Database**.
2. Clique em **"Criar banco de dados"**.
3. Escolha a localização (qualquer uma próxima do Brasil, ex. `southamerica-east1`).
4. Em **"Regras de segurança iniciais"**, escolha **"Iniciar em modo de teste"** — isso já
   deixa leitura/escrita liberadas por 30 dias, tempo mais que suficiente para o semestre.
   Se preferir liberar por mais tempo, depois vá em **Regras** e use:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /sessions/{sessionId} {
         allow read, write: if true;
       }
     }
   }
   ```

   **Atenção:** essa regra deixa a coleção `sessions` pública (qualquer pessoa com o link
   do site consegue ler ou escrever nela). Como não há dados sensíveis — só nome, marca
   escolhida e acerto/erro — isso é aceitável para uma atividade de sala de aula, mas não
   use esse mesmo projeto Firebase para guardar outro tipo de informação.

## 3. Colar a configuração no arquivo

Abra o `README.md` (ou o `.html`, se estiver usando a versão de download) e localize,
perto do topo do `<script>`, este bloco:

```js
const FIREBASE_CONFIG = {
  apiKey: "COLE_AQUI_SUA_API_KEY",
  authDomain: "SEU-PROJETO.firebaseapp.com",
  projectId: "SEU-PROJETO",
  storageBucket: "SEU-PROJETO.appspot.com",
  messagingSenderId: "000000000000",
  appId: "1:000000000000:web:xxxxxxxxxxxxxxxxxxxxxxxx"
};
```

Substitua os 6 valores pelos que você copiou no passo 1. Salve, faça commit e suba
para o GitHub. Pronto — o site publicado já passa a gravar e mostrar tudo em tempo real.

## 4. Testar

1. Abra o site publicado (o link `.github.io`), digite um nome e jogue até o fim.
2. Em outro aparelho (ou aba anônima), abra o mesmo site, clique em **"Painel do professor"**
   e entre com:
   - Usuário: `andre.azevedo`
   - Senha: `psico2026!`
3. Você deve ver a resposta do primeiro aparelho aparecendo em tempo real.

## Sobre a senha do painel

O login do painel é uma trava simples, só para os alunos não abrirem o painel por engano
— **não é segurança de verdade**. Como o arquivo roda inteiro no navegador (não existe
"servidor" escondendo nada), qualquer pessoa com conhecimento técnico consegue ler o
usuário e a senha direto no código-fonte, ou ler os dados no Firestore diretamente. Para
uma atividade de sala de aula isso é razoável; não é adequado para dados sensíveis.

## Custos

O plano gratuito do Firebase (Spark) cobre 50 mil leituras e 20 mil escritas por dia —
muito acima do que uma turma gera em uma atividade dessas. Não é necessário cadastrar
cartão de crédito.
