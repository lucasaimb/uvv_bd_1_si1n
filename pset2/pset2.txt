/*QUESTÃO 01: prepare um relatório que mostre a média salarial dos funcionários
de cada departamento.*/

SELECT func.numero_departamento AS departamento,
	 AVG(salario) AS media_salarial 
FROM funcionario func
GROUP by func.numero_departamento
ORDER by func.numero_departamento asc;



/*QUESTÃO 02: prepare um relatório que mostre a média salarial dos homens e das
mulheres.*/

SELECT 
	funcionario.sexo AS sexo, 
	AVG(salario) AS media_salarial 
FROM funcionario
GROUP BY funcionario.sexo
ORDER BY funcionario.sexo;



/* QUESTÃO 03: prepare um relatório que liste o nome dos departamentos e, para
cada departamento, inclua as seguintes informações de seus funcionários: o nome
completo, a data de nascimento, a idade em anos completos e o salário.
*/

SELECT 
	nome_departamento AS departamento, 
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome completo', 
	data_nascimento, 
	TIMESTAMPDIFF(YEAR, data_nascimento,NOW()) AS idade, 
	salario
FROM funcionario func
INNER JOIN departamento dep 
ON func.numero_departamento = dep.numero_departamento;



/*QUESTÃO 04: prepare um relatório que mostre o nome completo dos funcionários, a idade em anos completos,
o salário atual e o salário com um reajuste que
obedece ao seguinte critério: se o salário atual do funcionário é inferior a 35.000 o
reajuste deve ser de 20%, e se o salário atual do funcionário for igual ou superior a
35.000 o reajuste deve ser de 15%.*/

SELECT 
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome completo',
 	TIMESTAMPDIFF(YEAR, data_nascimento,NOW()) AS idade, 
	salario AS 'salario atual',
CASE
	WHEN salario < 35000.00 THEN salario + salario * 0.20
	WHEN salario >= 35000.00 THEN salario + salario * 0.15
END 'salario atualizado'
FROM funcionario;



/*QUESTÃO 05: prepare um relatório que liste, para cada departamento, o nome
do gerente e o nome dos funcionários. Ordene esse relatório por nome do departamento
(em ordem crescente) e por salário dos funcionários (em ordem decrescente).*/

SELECT
	nome_departamento AS departamento,
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'funcionario',
	CASE 
		WHEN func.cpf = dep.cpf_gerente THEN 'gerente'
		ELSE 'funcionario'
	END AS 'cargo'
FROM funcionario func
INNER JOIN departamento dep
	ON func.numero_departamento = dep.numero_departamento
ORDER BY 
	nome_departamento ASC, 
	salario DESC;



/*QUESTÃO 06: prepare um relatório que mostre o nome completo dos funcionários que têm dependentes,
 o departamento onde eles trabalham e, para cada funcionário, também liste o nome completo dos dependentes,
 a idade em anos de cada dependente e o sexo (o sexo NÃO DEVE aparecer como M ou F, deve aparecer
como “Masculino” ou “Feminino”).*/

SELECT 
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome funcionario',
 	nome_departamento AS departamento,
	CONCAT('',nome_dependente, ' ', ultimo_nome) AS 'nome dependente',
 	TIMESTAMPDIFF(YEAR, dpdt.data_nascimento,NOW()) AS 'idade dependente',
	CASE
		WHEN dpdt.sexo = 'F' then 'Feminino'
		WHEN dpdt.sexo = 'M' then 'Masculino'
	END AS 'Sexo dependente'
FROM funcionario func
INNER JOIN dependente dpdt
	ON func.cpf = dpdt.cpf_funcionario
INNER JOIN departamento dep
 	ON func.numero_departamento = dep.numero_departamento;



/*QUESTÃO 07: prepare um relatório que mostre, para cada funcionário que NÃO
TEM dependente, seu nome completo, departamento e salário.*/

SELECT
 	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome funcionario',
	nome_departamento AS departamento,
	salario
FROM funcionario func
INNER JOIN departamento dep
	ON func.numero_departamento = dep.numero_departamento
LEFT OUTER JOIN dependente dpdt
	ON func.cpf = dpdt.cpf_funcionario
WHERE dpdt.cpf_funcionario is null;



/*QUESTÃO 08: prepare um relatório que mostre, para cada departamento, os projetos desse departamento e
 o nome completo dos funcionários que estão alocados em cada projeto. Além disso inclua o número de horas
 trabalhadas por cada funcionário, em cada projeto.*/  

SELECT DISTINCT
	nome_departamento AS departamento,
	nome_projeto AS projeto,
	CONCAT('', primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome funcionario',
	SUM(horas)
FROM funcionario func
INNER JOIN departamento dep
	ON func.numero_departamento = dep.numero_departamento
INNER JOIN projeto pjt
	ON pjt.numero_departamento = dep.numero_departamento
INNER JOIN trabalha_em trb
	ON trb.numero_projeto = pjt.numero_projeto
	WHERE func.cpf = trb.cpf_funcionario
	GROUP BY cpf, nome_projeto
	ORDER BY primeiro_nome;



/*QUESTÃO 09: prepare um relatório que mostre a soma total das horas de cada
projeto em cada departamento. Obs.: o relatório deve exibir o nome do departamento,
 o nome do projeto e a soma total das horas.*/

SELECT 
	nome_departamento,
	nome_projeto,
	pjt.numero_projeto,
	sum(horas) AS 'horas departamento'
FROM projeto pjt
INNER JOIN departamento dep
	ON dep.numero_departamento = pjt.numero_departamento
INNER JOIN trabalha_em trb
	ON trb.numero_projeto = pjt.numero_projeto
GROUP BY pjt.numero_projeto;



/*QUESTÃO 10: prepare um relatório que mostre a média salarial dos funcionários
de cada departamento.*/

SELECT 
	nome_departamento AS 'nome departamento',
	dep.numero_departamento AS 'numero departamento',
	avg(salario) AS 'média salarial'
FROM departamento dep
INNER JOIN funcionario func
	ON dep.numero_departamento = func.numero_departamento
GROUP BY func.numero_departamento
ORDER BY avg(salario);


 
/*QUESTÃO 11: considerando que o valor pago por hora trabalhada em um projeto
é de 50 reais, prepare um relatório que mostre o nome completo do funcionário, o
nome do projeto e o valor total que o funcionário receberá referente às horas 
trabalhadas naquele projeto.*/

SELECT
	concat('', primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS funcionario,
	nome_projeto AS projeto,
	horas * 50.0 AS 'R$ por horas no projeto'
	FROM funcionario func
INNER JOIN trabalha_em trb
	ON func.cpf = trb.cpf_funcionario
INNER JOIN projeto pjt
	ON pjt.numero_projeto = trb.numero_projeto;



/*QUESTÃO 12: seu chefe está verificando as horas trabalhadas pelos funcionários
nos projetos e percebeu que alguns funcionários, mesmo estando alocadas à algum
projeto, não registraram nenhuma hora trabalhada. Sua tarefa é preparar um relatório 
que liste o nome do departamento, o nome do projeto e o nome dos funcionários
que, mesmo estando alocados a algum projeto, não registraram nenhuma hora trabalhada.*/

SELECT DISTINCT
	nome_departamento AS departamento,
	nome_projeto AS projeto,
	concat('', primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS funcionario
FROM funcionario func
INNER JOIN departamento dep
	ON dep.numero_departamento = func.numero_departamento
INNER JOIN projeto pjt
	ON dep.numero_departamento = pjt.numero_departamento
INNER JOIN trabalha_em trb
	ON trb.numero_projeto = pjt.numero_projeto
WHERE horas is null;



/*QUESTÃO 13: durante o natal deste ano a empresa irá presentear todos os funcionários e todos os dependentes (sim, a empresa vai dar um presente para cada
funcionário e um presente para cada dependente de cada funcionário) e pediu para
que você preparasse um relatório que listasse o nome completo das pessoas a serem
presenteadas (funcionários e dependentes), o sexo e a idade em anos completos
(para poder comprar um presente adequado). Esse relatório deve estar ordenado
pela idade em anos completos, de forma decrescente*/

SELECT
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS nome,
	sexo,
	TIMESTAMPDIFF(YEAR, data_nascimento,NOW()) AS idade
FROM funcionario
UNION
SELECT
	CONCAT('',nome_dependente, ' ', ultimo_nome),
	dpdt.sexo,
	TIMESTAMPDIFF(YEAR, dpdt.data_nascimento,NOW())
FROM dependente dpdt
INNER JOIN funcionario func
	ON func.cpf = dpdt.cpf_funcionario
ORDER BY idade;



-- QUESTÃO 14: prepare um relatório que exiba quantos funcionários cada departamento tem

SELECT 
	nome_departamento AS departamento,
	count(cpf) AS 'quantidade de funcionarios'
FROM funcionario func
INNER JOIN departamento dep
	ON dep.numero_departamento = func.numero_departamento
GROUP BY func.numero_departamento;



/*QUESTÃO 15: como um funcionário pode estar alocado em mais de um projeto,
prepare um relatório que exiba o nome completo do funcionário, o departamento
desse funcionário e o nome dos projetos em que cada funcionário está alocado.
Atenção: se houver algum funcionário que não está alocado em nenhum projeto,
o nome completo e o departamento também devem aparecer no relatório.*/

SELECT DISTINCT
	CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome) AS 'nome funcionario',
	nome_departamento AS departamento,
	nome_projeto AS projeto
FROM funcionario func
INNER JOIN departamento dep
	ON dep.numero_departamento = func.numero_departamento
INNER JOIN projeto pjt
	ON pjt.numero_departamento = func.numero_departamento
WHERE pjt.numero_departamento = func.numero_departamento OR pjt.numero_projeto is null
ORDER BY CONCAT('',primeiro_nome, ' ', nome_meio, ' ', ultimo_nome);
