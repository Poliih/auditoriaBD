-- Criação da tabela de produtos
create table produto (
	id int not null auto_increment,
	nome varchar(30) not null,
	estoque int not null,
	estoqueminimo int not null,
	usuario_inclusao varchar(50) not null,
	usuario_modificacao varchar(50) not null,
	constraint pk_produto primary key (id)
);

-- Criação da tabela de auditoria de produto
CREATE TABLE auditoriaproduto (
    id SERIAL PRIMARY KEY,
    acao VARCHAR(20),
    data_hora TIMESTAMP,
    usuario VARCHAR(50),
    id_produto INT,
    FOREIGN KEY (id_produto) REFERENCES produto(id)
);

-- Inserção de dados na tabela de produtos
INSERT INTO produto (nome, estoque, estoqueminimo, usuario_inclusao, usuario_modificacao)
VALUES
    ('Placa de Vídeo', 10, 5, 'João', 'Maria'),
    ('Monitor', 20, 10, 'Pedro', 'Thais'),
    ('Teclado', 15, 8, 'Ana', 'Julia');

-- Inserção de dados na tabela de auditoria de produto
INSERT INTO auditoriaproduto (acao, data_hora, usuario, id_produto)
VALUES
    ('inclusão', '2023-07-07 09:00:00', 'João', 1),
    ('alteração', '2023-07-07 09:15:00', 'Thais', 1),
    ('inclusão', '2023-07-07 10:30:00', 'Pedro', 2),
    ('alteração', '2023-07-07 10:45:00', 'Maria', 2),
    ('inclusão', '2023-07-07 11:30:00', 'Ana', 3),
    ('alteração', '2023-07-07 11:45:00', 'Julia', 3);

-- Criação do trigger para inclusão de produtos na tabela de auditoria
DELIMITER //
CREATE TRIGGER tr_produto_insert
AFTER INSERT ON produtos
FOR EACH ROW
BEGIN
    INSERT INTO auditoriaproduto (data_hora, acao, usuario, id_produto)
    VALUES (NOW(), 'inclusão', NEW.usuario_inclusao, NEW.id);
END //
DELIMITER ;

-- Criação do trigger para modificação de produtos na tabela de auditoria
DELIMITER //
CREATE TRIGGER tr_produto_update
AFTER UPDATE ON produtos
FOR EACH ROW
BEGIN
    INSERT INTO auditoriaproduto (data_hora, acao, usuario, id_produto)
    VALUES (NOW(), 'alteração', NEW.usuario_modificacao, NEW.id);
END //
DELIMITER ;

-- Consulta que retorna as informações solicitadas
SELECT
    p.id,
    p.nome,
    ap_inclusao.data_hora AS data_hora_inclusao,
    p.usuario_inclusao,
    ap_modificacao.data_hora AS data_hora_modificacao,
    ap_modificacao.usuario AS usuario_modificacao
FROM
    produto p
LEFT JOIN (
    SELECT
        id_produto,
        data_hora,
        usuario,
        ROW_NUMBER() OVER (PARTITION BY id_produto, acao ORDER BY data_hora) AS rn
    FROM
        auditoriaproduto
    WHERE
        acao = 'inclusão'
) ap_inclusao ON ap_inclusao.id_produto = p.id AND ap_inclusao.rn = 1
LEFT JOIN (
    SELECT
        id_produto,
        data_hora,
        usuario,
        ROW_NUMBER() OVER (PARTITION BY id_produto, acao ORDER BY data_hora DESC) AS rn
    FROM
        auditoriaproduto
    WHERE
        acao = 'alteração'
) ap_modificacao ON ap_modificacao.id_produto = p.id AND ap_modificacao.rn = 1;

