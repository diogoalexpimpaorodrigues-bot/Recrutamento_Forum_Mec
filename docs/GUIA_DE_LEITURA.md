# Guia para compreender e apresentar o projeto

## 1. Começar pelo comportamento

Executa a aplicação e percorre registo, entrada, um desafio, um resgate e o histórico. Experimenta também uma resposta errada e um brinde sem saldo. Depois atualiza a página e volta a entrar. A diferença entre estado local e dados persistidos fica mais clara quando se observa o comportamento.

## 2. Seguir um pedido de crédito

1. `Challenge.jsx` guarda o índice da resposta selecionada, sem alterar pontos.
2. `Dashboard.jsx` pede a operação ao hook `usePassport`.
3. `api.js` envia JSON e o token CSRF; a sessão segue num cookie HttpOnly.
4. `views.claim` verifica sessão, formato e eventual troca de conta.
5. `claim_points`, em `services.py`, verifica a resposta e abre uma transação.
6. A base atualiza saldo/revisão e cria o movimento. Se algo falhar, reverte ambos.
7. `snapshot` lê uma fotografia consistente; React apresenta o resultado recebido.

O mesmo desenho aplica-se ao resgate, com a condição adicional `balance >= cost` na própria atualização SQL.

## 3. Conseguir explicar as decisões

- **Porquê Django auth?** Para reutilizar hashing, sessões e validação mantidos pelo framework.
- **Porquê não calcular o saldo no React?** O navegador pode ser alterado pelo utilizador; só o servidor pode autorizar uma operação.
- **Porquê uma transação se há restrições?** A unicidade impede repetir o movimento, mas é a transação que impede deixar o saldo alterado quando a inserção falha.
- **Porquê uma expressão `F`?** A base faz o cálculo sobre o valor atual, em vez de guardar um resultado calculado a partir de uma leitura antiga.
- **Porquê revisão além de saldo?** O saldo pode voltar ao mesmo valor depois de crédito e débito; a revisão identifica a ordem das mudanças.
- **Porquê não repetir um pedido com erro de rede?** O servidor pode já ter confirmado a operação. Primeiro consulta-se o estado real.
- **Porquê SQLite?** Simplifica a instalação local; as escritas são serializadas e a solução tem limites de carga explícitos.
- **Porquê não JWT/localStorage?** Uma sessão de mesma origem resolve o requisito com menos mecanismos e sem guardar um token de autenticação acessível ao JavaScript.
