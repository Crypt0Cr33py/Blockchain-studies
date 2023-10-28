
// SPDX-License-Identifier: MIT
//Contrato Hello World
pragma solidity ^0.8.7;

contract HelloWorld {
    string public mystring = "hello world";
}
pragma solidity ^0.8.7;

//Tipos de dados
// Dados são caracterizados como valores ou referencias
// valores são bool ou int
// referencias são arrays e ponteiros

contract ValueTypes{
    bool public b = true;
    //unsigned é 0 ou positivos
    uint public u = 123;
    //uint = uint256 -> 0 to 2**256-1
    //também tem uint8, uint16, etc -> em passos de oito
    int public i = -123;
    //int é a mesma coisa que uint
    int public minInt = type(int).min;
    int public maxInt = type(int).max;
    //a variavel address pega um endereço de um valor no bloco
    //a variavel bytes pega um valor em bytes
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract FunctionIntro{
    function add(uint x, uint y) external pure returns (uint){
    //external significa que depois que o contrato é feito, é possível chamar a função
    //pure significa que é readonly, não escreve na blockchain
    return x +y;
    }
    function sub(uint x, uint y) external pure returns (uint){
        return x - y;
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract StateVariables {
    uint public myUint = 123;
    //variaveis de estado são escritas fora de função
    function foo() external {
        uint notStateVariable = 456;
    //função local, só é viva durante o momento da função
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract GlobalVaribles {
    function globalVars() external view returns (address, uint, uint) {
        address sender = msg.sender; //variavel global que armazena o endereço de uma conta
        uint timestamp = block.timestamp; //tempo de execução do bloco
        uint blocknum = block.number; //número do bloco
        return (sender, timestamp, blocknum);
   }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract ViewAndPureFunctions {
    //a diferença é que view pode ler coisas da block chain e pure não le nada
    uint public num;

    function viewFunc () external view returns (uint){
        return num;
        //só retorna mas não modifica e le a variavel state
    }

    function pureFunc () external pure returns (uint){
        return 1;
        //só retorna o 1, se le só da função, não é view
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract Counter {
    uint public cont;

    function inc() external {
        cont += 1;
    }
    function dec() external {
        cont -= 1;
    }
}
//SPDX-License-Identifier: UNLICENSE
pragma solidity ^0.8.7

contract DefaultVariables{
    bool public b; //false
    uint public u; //uint valor é zero
    int public i; //valor 0
    address public a; // 0x0000000000000000000000000000000000000000
    bytes32 public b; // 0x0000000000000000000000000000000000000000000000000000000000000000
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7

contract Constants{
    address public constant MY_ADDRESS = 0x0310310404014010420203013014;
    uint public contract MY_UINT = 123; //Constante é em letra maiuscula eu acho
}
//custa menos gas pra fazer deploy de constante do que de variaveis normais (tipo state)

//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract IfElse{
    function exemple(uint _x) external pure returns (uint) {
        if (_x < 10){
            return 1;
        }else if (_x <20){
            return 2;
        }else {
            return 3; //o else da pra escrever omitindo o else e só colocando o return
        }
    }

    function ternary(uint _x) external pure returns (uint){
        //if (_x <10){
        //    return 1;
        //}
        //return 2; //aqui o exemplo do else omitido
        return _x < 10 ? 1 : 2; //operador if else reduzido
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract ForAndWhileLoops{
    function loops() external pure {
        for (uint i = 0; i < 10; i++){
            //codigo
            if (i = 3){
                continue;
                //pula no i = 3
            }
            //mais código
            if (i=5){
                break;//finaliza o loop
            }
        }
        uint j = 0;
        while (j < 10){
            //codigo
            j++;
        }
    }

    function sum(uint _n) external pure returns (uint) {
        uint s;
        for (uint i=1; i <= _n; i++){
            s += i;
        }
        return s;
    }
    //quanto mais loops, mais gas
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//chamar uma função num contrato, tem 3 formas de tratar um erro
//require, revert e assert

contract Error{
    function testeRequire(uint _i) public pure { //o _i é uma variável de input
        require(_i <= 10, "i > 10"); //se for maior que 10 o require printa a msg
        //codigo
    }
    function testeRevert(uint _i) public pure {
        if (_i > 10){
            revert("i>10");  //validador de if e else com a mensagem
        }
    }
    uint public num = 123;

    function testeAssert() public view{
        assert(num == 123); //validador que deve sempre retornar true
    }
    error MyError(address caller, uint i); // da pra logar infos no error

    function testeCustomError(uint _i) public view{//custom error poupa o preço do gás
        if (_i < 10){
            revert MyError(msg.sender, _i); //mensagem muito grande custa muito gas
        }
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//funtion modifier permite o reuso de código

contract FunctionModifier{
    bool public paused;
    uint public count;

    function setPause(bool _paused) external {
        paused = _paused;
    }

    modifier whenNotPaused(){
        require(!paused, "paused");
        _; //chama a funçao que o modifier envolve
    }

    function inc() external whenNotPaused {//aqui
        //require(!paused, "paused");//se não tiver pausado continua, se não paused
        count+=1;
    }
    function dec() external whenNotPaused {
        //require(!paused, "paused");
        //da pra remover o requise porque já ta no modifier
        count-=1;
    }

    modifier cap(uint _x){//modifier com um input
        require (_x < 100, "x >= 100");
        _;
    }

    function incBy(uint _x) external whenNotPaused cap(_x) {//passar o cap
        //require (_x < 100, "x >= 100");
        count += _x
    }

    modifier sandwich(){//modifier sendo chamado antes e depois de chamar a função
        //code
        count +=10;
        _;//chama o foo
        //mais codigo
        count*=20
    }
    function foo() external sandwich{
        count +=1;
    }
}
//SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.7;

contract Constructor {//inicia variaveis de estado e é chamado quando o contrato é chamado
    address public owner;
    uint public x;

    constructor(uint _x){
        owner = msg.sender;//owner é a conta que fez o deploy do contrato
        x = _x;
    }
}
//SPDX-License-Identifier: UNICENSED

pragma solidity ^0.8.7;

contract Owneable{
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner(){
        require(msg.sender ==owner, "not owner");
        _;
    }

    function setOwner(address _newOwner) external onlyOwner {
        require(_newOwner != address(0), "invalid address");
        owner = _newOwner;
    }

    function onlyOwnerCanCall() external onlyOwner{
        //code
    }

    function anyOneCanCall() external {
        //code
    }
}
//SPDX-Licensed-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract FunctionOutputs{
    function returnMany() public pure returns (uint, bool) {
        return (1, true);
    }
    function names() public pure returns (uint x, bool b) {
        return (1, true);
    }    
    function assign() public pure returns (uint x, bool b) {
        x=1;
        b = true;// retorna os valores, sem precisar do returna
    }
    function destructingAssignments () public pure {
        (uint x, bool b) = returnMany(); //captura os valores de um output e associa a função
        (, bool _b) = returnMany;
    }
}
//SPDX-Licensed-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract ArrayShift{
    uint[] public arr;

    function exemple() public{
        arr = [1,2,3];
        delete arr[1]; //deleta o segundo elemento do array, tornando ele 0
    }
    //a fora mais fácil de remover um elemento sem modificar os outros é com array shifiting
    //consistem em copiar o elemento que vc não quer remover pro index
    //e deletar o ultimo elemento, com pop, ex:
    //[1,2,3,4,5,6] -- remove(2) --> [1,2,4,5,6,6] --> [1,2,4,5,6]
    function remove(uint _index) public{
        require(_index < arr.length, "index out of bound");

        for (uint i = _index; i < arr.length -1; i++){//seta um index que é menor que o legnth
            arr[i] = arr[i+1]; //adiciona 1 a partir do index
        }
        arr.pop();        
    }
    function test() external{
        arr = [1,2,3,4,5];
        remove(2);
        assert(arr[0] == 1);
        assert(arr[1] == 2);
        assert(arr[2] == 4);
        assert(arr[3] == 5);
        assert(arr.length == 4);

        arr=[1];
        remove(0);
        assert(arr.length == 0);
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract ArrayReplaceLast{
    uint[] public arr;

    //[1,2,3,4] -- remove(1) --> [1,4,3]
    //[1,4,3] -- remove(2) --> [1,4]
    function remove(uint _index) public {
        arr[_index] = arr[arr.length -1];
        arr.pop();
    }
    function test() external{
        arr = [1,2,3,4];
        remove(1);

        assert(arr.length == 3);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
        assert(arr[2] == 3);

        remove(2);
        assert(arr.length == 2);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//mapping é um dicionario em python
contract Mapping{
    mapping(address => uint) public balances; //com a flecha diz key e value e balances é o nome
    mapping(address => mapping(address => bool)) public isFriend;

    function exemples() external {
        balances[msg.sender] = 123;
        uint bal = balances[msg.sender];
        uint bal2 = balances[address(1)]; //0 -> valor default de uint

        balances[msg.sender] += 456;

        delete balances[msg.sender]; //deleta e reseta pra 0

        isFriend[msg.sender][address(this)] = true;
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;


contract IterableMapping{
    mapping(address => uint) public balances;
    mapping(address => bool) public inserted;
    address[] public keys;

    function set(address _key, uint _val) external{
        balances[_key] = _val;

        if (!inserted[_key]) {
            inserted[_key] = true;
            keys.push(_key);
        }
    }

    function getSize() external view returns (uint){
        return keys.length;
    }

    function first() external view returns (uint){
        return balances[keys[0]];

    }
    function last() external view returns (uint){
    return balances[keys[keys.length -1]];
    }
    function get(uint _i) external view returns (uint){
    return balances[keys[_i]];
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;


contract Structs {
    //Structs permite que agrepemos dados juntos
    struct Car {
        string model;
        uint year;
        address owner;
    }
    Car public car; //nomeia como uma variavel de estado
    Car[] public cars; //cria um array
    mapping(address => Car[]) public carsByOwner; //cria um mapa

    function exemples() external {
        Car memory toyota = Car("Toyota", 1990, msg.sender); //inicializando
        //memory é pra criar uma variael local
        Car memory lambo = Car({year: 1980, model: "lambo", owner: msg.sender}); //dessa forma, pode trocar a ordem
        Car memory tesla; //vazio
        tesla.model="Tesla";
        tesla.year= 2010;
        tesla.owner = msg.sender;

        cars.push(toyota);
        cars.push(lambo);
        cars.push(tesla);
        //criando o array, da pra armazenar os valores em state variable, fugindo do memory

        cars.push(Car("Ferrari", 2020, msg.sender)); //Coloca no array direto

        //Car memory _car = cars[0]; //carregando em memoria
        //_car.model;
        //_car.year;
        //_car.owner;

        Car storage _car = cars[0]; //queremos modificar os valores no contrato com storage
        _car.year = 1999;
        delete _car.owner; //reseta o valor

        delete cars[1]; //reseta o valor do elemento 2 do array
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract Enum { //Enums permitem expressar diversas escolhas
    enum Status {
        None,
        Pending,
        Shipped,
        Completed,
        Rejected,
        Canceled
    }
    Status public status; //Cria uma variavel de estado

    struct Order{
        address buyer;
        Status status;
    }

    Order [] public orders; //array de enum

    function get() view external returns (Status) {
        return status;//retorna um enum
    }

    function set(Status _status) external {
        status = _status; //faz um update de um enum de entrada pro enum
    }

    function ship() external {
        status = Status.Shipped; //muda um valor do enum

    }

    function reset() external{
        delete status; //faz virar o valor default
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//deploy de qualquer contrato

contract TestContract1{
    address public owner = msg.sender;

    function setOwner(address _owner) public {
        require(msg.sender == owner, "not owner");
        owner = _owner;
    }
}

contract TestContract2 {
    address public owner = msg.sender;
    address public value = msg.sender;
    uint public x;
    uint public y;

    constructor(uint _x, uint _y) payable {
        x = _x;
        y = _y;
    }
}

contract Proxy{
    event Deploy(address);

    fallback() external payable{} //declarado porque pode voltar ETH

    function deploy(bytes memory _code) external payable returns (address addr) {//payable pra conseguir mandar ether
        assembly{//necessita pra fazer deploy de contrato arbitrario
            //criar(v, p, n)
            //v = quantidade de ETH pra mandar (callvalue)
            //p = ponteiro na memoria pra começar o código -> começa depois de 32 bytes
            //n = tamanho do código -> armazenado nos primeiros 32 bytes (_code)
            addr := create(callvalue(), add(_code, 0x20), mload(_code)) //define uma variable aadress e retorna essa variavel
        }
        require(addr != address(0), "deploy failed");

        emit Deploy(addr);//emite o contrato logando o endereço 
    }

    function execute(address _target, bytes memory _data) external payable{
        (bool success, ) = _target.call{value: msg.value} (_data);
        require(success, "failed");
    }
}

contract Helper {// função pra ajudar a pegar os endereços
    function getBytecode1() external pure returns (bytes memory){
        bytes memory bytecode = type(TestContract1).creationCode;
        return bytecode;
    }

    function getBytecode2(uint _x, uint _y) external pure returns (bytes memory){
        bytes memory bytecode = type(TestContract2).creationCode;
        return abi.encodePacked(bytecode, abi.encode(_x, _y));
    }

    function getCalldata(address _owner) external pure returns (bytes memory){
        return abi.encodeWithSignature("setOnwer(address", _owner);
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//Data locations - storage, memory and calldata
//storage = statevariable
//memory ta armazenada na memoria
//calldata é pra input de função

contract DataLocations{
    struct MyStruct {
        uint foo;
        string text;
    }

    mapping(address => MyStruct) public myStructs;

    function exemples(uint[] memory y, string memory s) external returns (uint[] memory) {
        myStructs[msg.sender] = MyStruct({foo: 123, text: "bar"});

        MyStruct storage myStruct = myStructs[msg.sender];
        myStruct.text = "foo";//muda o dado

        MyStruct memory readOnly = myStructs[msg.sender];
        readOnly.foo = 456;//le o dado

        _internal(y)

        uint[] memory memArr = new uint[](3);//inicializa um array estatico em memoria
        memArr[0] = 234;
        return memArr;
    }

    function _internal(uint[] calldata y ) private{//modifica o valor sem copiar a variave, só fazendo referencia e salva gas
        uint x = y[0];
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract SimpleStorage{
    string public text;
    //calldata é ponteiro
    function set(string calldata _text) external{
        text = _text;
    }
    function get() external view returns (string memory){//copia a variavel pra memória e retorna o valor
        return text;
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract TodoList{
    struct Todo{
        string text;
        bool completed;

    }

    Todo [] public todos;

    function create(string calldata _text) external{
        todos.push(Todo({
            text: _text,
            completed: false
        }));
    }

    function updateText(uint _index, string calldata _text) external{
        todos[_index].text = _text;

        //Todo storage todo = todos[_index];
        //todo.text = _text; -> mesma forma, mas pra mudar vários index é melhor
    }
    function get(uint _index) external view returns (string memory, bool) {
        Todo memory todo = todos[_index];
        return (todo.text, todo.completed);
    }

    function toggleCompleted(uint _index) external{
        todos[_index].completed = !todos[_index].completed; //alterna o booleano do completed
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract Event {
    //loga dados na block chain sem retornar o dado
    event Log(string message, uint val);
    //só mais de 3 exemplos podem ser indexados
    event IndexLog(address indexed sender, uint val);

    function exemple() external{ //função transacional, porque escreve na blockchain
        emit Log("foo", 1234); //emit é usado pra escrever o event
        emit IndexLog(msg.sender, 789);
    }
    event Message(address indexed _from, address indexed _to, string message);
    function sendMessage(address _to, string calldata massage) external {
        emit Message(msg.sender, _to, message);
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//inhertance serve pra usar o código de A em B
contract A{
    function foo() public pure virtual returns (string memory) {
        return "A";
    }

    function bar() public pure virtual returns (string memory) {
        return "A";
    }

    //more code
    function baz() public pure virtual returns (string memory) {//B vai herdar, com o valor de A, sem modificar
        return "A";
    }
}

contract B is A{//pro B herdar A
    function foo() public pure override returns (string memory) {//override serve pra fazer o filho herdar
        return "B";
    }

    function bar() public pure override virtual returns (string memory) {
        return "B";
    }
}

contract C is B{//C vai herdar baz também
    function bar() public pure override  returns (string memory) {
        return "C";
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//quando um contrato herda mais de 1, é importante a ordem
//o contrato mais baselar, tem que ser derived

contract X{
    function foo() public pure virtual returns (string memory) {
        return "X";
    }

    function bar() public pure virtual returns (string memory) {
        return "X";
    }

    //more code
    function x() public pure returns (string memory) {//B vai herdar, com o valor de A, sem modificar
        return "X";
    }
}

contract Y is X{
    function foo() public pure virtual override returns (string memory) {
        return "Y";
    }

    function bar() public pure virtual override returns (string memory) {
        return "Y";
    }

    //more code
    function y() public pure  returns (string memory) {//B vai herdar, com o valor de A, sem modificar
        return "Y";
    }
}

contract Z is X, Y{ //tem que ser nessa ordem, porque o X é mais baselar, se não não compila
    function foo() public pure override(X,Y) returns (string memory) {//precisa do override
        return "Z";
    }

    function bar() public pure override(X,Y) returns (string memory) {
        return "Z";
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract S{
    string public name;

    constructor(string memory _name){
        name = _name;
    }
}

contract T{
    string public text;

    constructor (string memory _text){
        text = _text;
    }
}

contract U is S("s"), T("t"){//fazendo refrencia aos constructors

}

contract V is S, T{
    constructor(string memory _name, string memory _text) S(_name)T(_text){//passando parametros que serão iniciados no deploy do contrato pra função pai

    }
}

contract VV is S("s"), T {//S é estatico
    constructor(string memory _text) T(_text){

    }
}

// a ordem de inicialização é respeitand a herança
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract E{
    event Log(string message);

    function foo() public virtual {
        emit Log("E.foo");
    }

    function bar() public virtual {
        emit Log("E.bar");
    }
}

contract F is E{

    function foo() public virtual override {
        emit Log("F.foo");
        E.foo(); //chama a função foo do contrato E
    }

    function bar() public virtual override {
        emit Log("F.bar");
        super.bar();//super serve pra fazer referencia a função pai
    }
}

contract G is E{

    function foo() public virtual override {
        emit Log("G.foo");
        E.foo(); //chama a função foo do contrato E
    }

    function bar() public virtual override {
        emit Log("G.bar");
        super.bar();//super serve pra fazer referencia a função pai
    }
}

contract H is F, G{
    function foo() public override (F,G){
        F.foo(); //só chama a função diretamentamente
    }

    function bar() public override (F,G){
        super.bar();//chama todas as funções de super
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//visibility
//private - só interage o que estiver dentro do contrato
//internal - só dentro do contrato e contratos filhos
//public - dentro e fora do contrato
//external - somente de fora do contrato

contract VisibilityBase{
    uint private x=0;
    uint internal y=1;
    uint public z=;

    function privateFunc() private pure returns (uint){
        return 0;
    }
    function internalFunc() internal pure returns (uint){
        return 100;
    }
    function publicFunc() public pure returns (uint){
        return 200;
    }
    function externalFunc() external pure returns (uint){
        return 2000;
    }

    function examples() external view{
        x + y + z;

        privateFunc();
        internalFunc();
        publicFunc();
        //external não, porque só da pra chamar fora

        this.externalFunc(); //this faz com que de pra chamar uma função external NESTE contrato, mas gasta muito gás
    }
}

contract VisibilityChild is VisibilityBase{
    function examples2() external view{
        y + z; //não da pra chamar private
        internalFunc();
        publicFunc();
        //nao da pra chamar externo
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract Immutable{
    address public immutable owner = msg.sender;
    //cria uma constante que é inicializada quando o contrato é deployado 
    address public immutable owner1;

    constructor(){
        owner1 = msg.sender;
        //assim q declara normalmente uma immutable variable nos contrats normais
    }

    uint public x;
    function foo() external {
        require(msg.sender == owner);
        x+=1;
        //immutable gasta menos gás
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

contract Payable{//serve pra mandar e receber ether
    address payable public owner;//tem que vir depois do tipo

    constructor(){
        owner = payable(msg.sender);//tem que colocar como payable se não fica só como addres
    }

    function deposit() external payable{
    }

    function getBalance() external view returns (uint){
        return address(this).balance;
    }
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

//Fallback é executado ou quando a função não existe ou pra enviar diretamente o ETH

contract Fallback{
    event Log(string func, address sender, uint value, bytes data);

    fallback() external payable{
        emit Log("fallback", msg.sender, msg.value, msg.data);
    }//permite que receba ether mesmo que uma função não exista

    receive() external payable{
        emit Log("fallback", msg.sender, msg.value, "");
    }//executa quando um msg.data está vazio e quando ele ta declarado
}
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.7;

interface ICounter {
    function count() external view returns (uint);
    function inc() external;
}//Interface server pra fazer referencia a um contrato externo chamando suas funçõesa

contract CallInterface{
    uint public count;

    function exemples(address _counter) external {
        ICounter(_counter).inc();
        count = ICounter(_counter).count();
    }
}
