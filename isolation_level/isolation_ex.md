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


### 

