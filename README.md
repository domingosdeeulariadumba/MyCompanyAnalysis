# my-portfolio
pt:
No ficheiro minha_empresa, eu apresento alguns insights extraídos do Power BI apartir dos dados compilados no SSMS.
In the file minha_empresa, I present some insights extracted from Power BI from a Database built in SSMS.

pt: Abaixo tem um resumo dos insights obtidos e a seguir o script usado na criação da base de dados (mais detalhes no ficheiro em zip) | en: Below is a summary of the insights obtained, and the the Database script (more details in the zip file)
![image](https://user-images.githubusercontent.com/110714056/233502312-3244fd0e-2cc0-4df6-8d53-64b80a8f8e3a.png)

/*criação da base de dados | creating the database*/

CREATE DATABASE firstproject;
 
/*criação e inserção dos dados nas tabelas | creating and inserting the data into the tables*/


-- tabela de funcionários | employees table--

CREATE TABLE funcionario (
id_func INT IDENTITY (1,1) PRIMARY KEY,
nome VARCHAR (25),
sobrenome VARCHAR (25),
data_de_nascimento DATE,
sexo VARCHAR (1),
salario REAL,
id_sup INT,
id_dependencia INT);

-- alteração do nome da coluna 'iddependencia' na tabela funcionarios | renaming the 'iddependencia' column in employees table --
	EXEC sp_RENAME 'funcionario.id_dependencia', 'id_dep', 'COLUMN';

INSERT INTO funcionario (nome, sobrenome, data_de_nascimento, sexo, salario)
VALUES ('João',	'Paulo',	'1987/12/11', 'M', 26000),
('Manuel', 'Anastácio', '1998/02/21', 'M', 176000),
('Joana', 'Tomás', '1993/08/03', 'F', 123000),
('Maria', 'Kindambo', '1987/06/14', 'F', 119000),
('Marcolino', 'Filipe', '1967/09/15', 'M', 101000),
('José', 'Sapassa', '1991/01/06', 'M', 136900),
('Kituxi', 'Manuel', '1983/01/01', 'F',	146200),
('Marcelina', 'Mendes', '1990/03/29', 'F', 155500),
('Abigail', 'Trovoada', '2004/03/03', 'F', 417590),
('Kituxi', 'Manuel', '1981/06/14', 'M', 174100),
('Lourenço',	'Camundai',	'1975/05/05', 'M', 183400),
('Nahari', 'Freitas', '1973/10/16',	'F', 192700),
('Luís', 'Brito', '1998/12/19', 'M', 202000),
('Almir', 'Proença', '1964/03/25', 'M',	211300),
('Nina', 'Abreu', '2003/03/03', 'F', 220600),
('Joelson',	'Filipe', '1999/07/14', 'M', 229900),
('Aline', 'Bunga', '1977/09/18', 'F', 239200),
('Raquel', 'Massoxi', '1986/08/17', 'F', 152960),
('Rui',	'Pemba', '1958/02/23', 'M',	383400),
('Ofília', 'Muaca', '2005/04/29', 'F',195000);

			-- substituição de nomes duplicados| replacing duplicate names --
			UPDATE funcionario
			SET nome='Amélia' WHERE id_func=7;
			UPDATE funcionario
			SET sobrenome='Saramago' WHERE id_func=7;

			-- declarando o 'idsup' como FOREIGN KEY na tabela de funcionários | declaring 'idsup' as FOREIGN KEY in employees table--
			ALTER TABLE funcionario
			ADD FOREIGN KEY (id_sup) REFERENCES funcionario(id_func);

			-- atualização do atributo 'idsup' na tabela funcionarios | updating 'idsup' in the employees table --
		UPDATE funcionario
		SET id_sup = 9 WHERE id_func IN (1, 7, 8, 15, 16, 17, 18, 19);
		UPDATE funcionario
		SET id_sup = 1 WHERE id_func = 20;
		UPDATE funcionario
		SET id_sup = 16 WHERE id_func IN (2, 3, 4, 6);
		UPDATE funcionario
		SET id_sup = 17 WHERE id_func IN (10, 11, 12, 14);
		UPDATE funcionario
		SET id_sup = 19 WHERE id_func IN (5, 13);


		-- tabela de dependencias | branches table--

CREATE TABLE dependencia (
id_dep INT IDENTITY (100,1) PRIMARY KEY,
dependencia VARCHAR (30),
id_gestor INT,
data_de_inicio_gestor DATE,
FOREIGN KEY (id_gestor) REFERENCES funcionario (id_func) ON DELETE SET NULL);

				
		-- declarando o 'id_dep' como FOREIGN KEY na tabela de funcionário | declaring 'id_sup' as FOREIGN KEY in employee table--
		ALTER TABLE funcionario ADD FOREIGN KEY (id_dep)
		REFERENCES dependencia (id_dep) ON DELETE SET NULL;


INSERT INTO dependencia (dependencia, id_gestor, data_de_inicio_gestor)
VALUES ('Luanda', 9, '1998-02-11'),
('Huambo', 16, '2000-02-21'),
('Benguela', 17, '1999-11-13'),
('Sumbe', 19, '2007-06-21');

		-- atualização da Foreign Key 'id_dep' na tabela funcionario | updating the Foreign Key 'id_dep' in the employee table --	
		UPDATE funcionario
		SET id_dep = 100 WHERE id_sup IN (9, 1);
		UPDATE funcionario
		SET id_dep = 100 WHERE id_func = 9;
		UPDATE funcionario
		SET id_dep = 101 WHERE id_sup = 16;
		UPDATE funcionario
		SET id_dep = 102 WHERE id_sup = 17;
		UPDATE funcionario
		SET id_dep = 103 WHERE id_sup = 19;


-- tabela de clientes | customers table--

CREATE TABLE cliente (
id_cliente INT IDENTITY (200,1) PRIMARY KEY,
cliente VARCHAR (35),
id_dep INT,
FOREIGN KEY (id_dep) REFERENCES dependencia (id_dep) ON DELETE SET NULL);

INSERT INTO cliente (cliente, id_dep)
VALUES ('TrasCorp LDA',	100), ('AngoTI SA',	102),
('ZIcon SA', 101), ('Orizeu e Filhos LDA',	103),
('Cassandra LDA', 103), ('Boom LDA', 101),
('Cléusia Sabores SA',	103);

-- tabela de relacionamentos entre funcionários e clientes | 'work with' relationship table between employees and customers --

CREATE TABLE cliente_vs_funcionario (
id_func INT,
id_cliente INT,
vendas REAL,
PRIMARY KEY (id_func, id_cliente),
FOREIGN KEY (id_func) REFERENCES funcionario (id_func) ON DELETE CASCADE,
FOREIGN KEY (id_cliente) REFERENCES cliente (id_cliente) ON DELETE CASCADE);

INSERT INTO cliente_vs_funcionario (id_func, id_cliente, vendas)
VALUES (3, 200, 1965000), (7, 201, 303005), (19, 202, 4536800),
(13, 203, 4840068), (9,	206, 25968), (7, 204, 7411868),
(1,	206, 697768), (8, 205, 9983668), (8,	201, 1269568);


-- tabela de fornecedores | suppliers table--

CREATE TABLE fornecedor (
id_dep INT,
fornecedor VARCHAR (30),
suprimento VARCHAR (35),
FOREIGN KEY (id_dep) REFERENCES dependencia (id_dep) ON DELETE CASCADE);

INSERT INTO fornecedor (id_dep, fornecedor, suprimento)
VALUES (100, 'NCR',	'Computadores'), (103, 'SISTEC', 'CPUs'),
(102, 'NCR', 'Papéis'), (101, 'WORTEN', 'Router'), 
(100, 'BaobaBay', 'Telemóveis'), (100,	'WORTEN', 'Routers'),
(100, 'SISTEC', 'Teclados'), (101,	'NCR',	'Computadores'), 
(100, 'NCR', 'Teclados');
