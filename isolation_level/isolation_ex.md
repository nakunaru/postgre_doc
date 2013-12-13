# �������x���̎���

�Q�l�F
http://www.postgresql.jp/document/9.3/html/transaction-iso.html  
http://www.postgresql.jp/document/9.3/html/sql-set-transaction.html  


## �T�|�[�g���Ă��镪�����x��

| �������x��                 | �_�[�e�B���[�h | �����s�\�ǂݎ��   |�t�@���g�����[�h  |
|:---------------------------|:---------------|:-------------------|:-----------------|
| READ UNCOMMITED            | �Ȃ� ��1       | ����               | ����             |
| READ COMMITED (�f�t�H���g) | �Ȃ�           | ����               | ����             |
| REPEATABLE READ            | ���S           | ���S               | ����             |
| SERIALIZABLE               | ���S           | ���S               | ���S             |

��1 READ UNCOMMITED�͎���READ COMMITED�Ɠ�������ƂȂ�


## �������x���ɂ�铯�����s���̎���

### �T���v���e�[�u���p��

    ```
    DROP TABLE IF EXISTS isotab;
    
    CREATE TABLE isotab
    (no int,
     name varchar(10));
    
    INSERT INTO isotab VALUES(10, 'aaa');
    INSERT INTO isotab VALUES(20, 'bbb');
    INSERT INTO isotab VALUES(30, 'ccc');
    INSERT INTO isotab VALUES(40, 'ddd');
    INSERT INTO isotab VALUES(50, 'eee');
    INSERT INTO isotab VALUES(60, 'fff');
    INSERT INTO isotab VALUES(70, 'ggg');
    INSERT INTO isotab VALUES(80, 'hhh');
    INSERT INTO isotab VALUES(90, 'jjj');
    ```

### READ COMMITED + UPDATE

1. session A:  

    ```
    BEGIN;
    SELECT * FROM isotab;
    
    no | name
    ---+------
    10 | aaa
    20 | bbb
    30 | ccc
    40 | ddd
    50 | eee
    60 | fff
    70 | ggg
    80 | hhh
    90 | jjj
    ```

2. session B:  

    ```
    BEGIN;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    
    UPDATE isotab SET name = 'EEE' WHERE no = 50;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- �ύX�����Z�b�V�����͕ύX��̒l���擾
    ```

3. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <- READ COMMITED�̃Z�b�V�����͕ύX�O�̃f�[�^���擾
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    ```

4. session B:  

    ```
    COMMIT;
    ```

5. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- �R�~�b�g������READ COMMITED�̃Z�b�V��������ύX��̃f�[�^���擾�ł���
    
    COMMIT;
    ```

+ READ COMMITED�̓��b�N�����Ȃ�


### REPEATABLE READ + UPDATE

1. session A:  

    ```
    BEGIN;
    SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
    
    SELECT * FROM isotab;
    ```

2. session B:  

    ```
    BEGIN;
    UPDATE isotab SET name = 'EEE' WHERE no = 50;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- �ύX��̒l
    ```

3. session A:  

    ```
    SELECT * FROM isotab;

     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <- �ύX�O�̒l
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    ```

4. session B:  

    ```
    COMMIT;
    ```

5. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <-�ύX�O�̒l
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    
    COMMIT;
    
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- �ύX��̒l
    ```

��REPEATABLE READ��SELECT���́A�g�����U�N�V�����J�n���_�̃R�~�b�g�ς݃X�i�b�v�V���b�g����������  
��REPEATABLE READ��UPDATE�����DELETE�́A�g�����U�N�V�����J�n���_�̃R�~�b�g�ς݃X�i�b�v�V���b�g�ɑ΂��X�V�����݂�B  
�@�X�V���ɁA���̃Z�b�V�����ɂ��X�V�Ń��b�N����Ă����ꍇ�A���b�N������҂��A���[���o�b�N���ꂽ�ꍇ�͍X�V�𑱍s�A�R�~�b�g���ꂽ�ꍇ��
�@�u�����X�V�̂��ߒ��񉻃A�N�Z�X���ł��܂���ł����v  
�@�G���[���o�͂��A�X�V�����s������B  

�Q�l�Fhttp://www.postgresql.jp/document/9.3/html/transaction-iso.html  



