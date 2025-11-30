# KipuBank

O objetivo do presente é fazer uma evolução do código original de EXEMPLO do i3arba, obedecendo às diretrizes da atividade.
As "melhorias" são aplicadas em commits consecutivos, que refletem tanto no texto quanto no código as diretrizes contempladas.

## Adequação às diretrizes do capítulo 2 
Embora esteja dentro das maioria das especificações, há alguns detalhes que requereram ajustes:

1. AMOUNT_PER_WITHDRAW deve ser immutable (e não constant)  
&nbsp;&nbsp;&nbsp;Situação presente no código:  
&nbsp;&nbsp;&nbsp;`uint256 constant AMOUNT_PER_WITHDRAW = 1 * 10 ** 16;`  
&nbsp;&nbsp;&nbsp;Alteração executada:  
&nbsp;&nbsp;&nbsp;`uint256 public immutable AMOUNT_PER_WITHDRAW;`  
&nbsp;&nbsp;&nbsp;Feita a inicialização no construtor:  
&nbsp;&nbsp;&nbsp;`AMOUNT_PER_WITHDRAW = 1 * 10 ** 16;`  

2. NatSpec obrigatória ausente no constructor  
&nbsp;&nbsp;&nbsp;Situação presente no código:  
```
    /*///////////////////////
           FUNCTIONS
    ///////////////////////*/
    constructor(uint256 _bankCap) {
```
&nbsp;&nbsp;&nbsp;Alteração executada:  
```
    /*///////////////////////
           FUNCTIONS
    ///////////////////////*/
    /// @notice Inicializa o contrato definindo o limite global de depósitos.
    /// @param bankCap Limite total de ETH que o contrato pode receber.
    constructor(uint256 _bankCap) {
```

3. Evento de retirada emitido antes da chamada externa (call)  
&nbsp;&nbsp;&nbsp;Situação presente no código:  
```
    function _processTransfer(uint256 _amount) private {
        emit KipuBank_SuccessfullyWithdrawn(msg.sender, _amount);

        (bool success, bytes memory data) = msg.sender.call{value: _amount}("");
        if (!success) revert KipuBank_TransferFailed(data);
    }
```  
&nbsp;&nbsp;&nbsp;Alteração executada:  
```
    function _processTransfer(uint256 _amount) private {

        (bool success, bytes memory data) = msg.sender.call{value: _amount}("");
        if (!success) revert KipuBank_TransferFailed(data);

        emit KipuBank_SuccessfullyWithdrawn(msg.sender, _amount);
    }
```  
Agora o evento de saque é emitido depois da execução e checagem da transferência.
