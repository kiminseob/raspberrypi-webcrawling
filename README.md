# facebook 로그인 & 크롤링한 정보를 텔레그램 봇 API를 통해 채팅방으로 전달하기.
* facebook에 로그인하여 웹 문서를 크롤링하여 필요한 데이터를 텔레그램 봇 API를 이용해 보내줍니다.

# 샘플코드
```
#텔레그렘 봇의 API 키를 가져옵니다.
bot = telegram.Bot(token = '봇 토큰 입력.')
chat_id =  메시지 받을 텔레그램 채팅 아이디 입력.

def main(username, password):

    logging.basicConfig(filename='imgur2fb.log', level=logging.DEBUG)

    with requests.Session() as session:
        uid, dtsg = login(session, username, password)
  
def login(session, username, password):

    #페이스북 홈페이지
    response = session.get('https://facebook.com')

    # Construct the DOM
    dom = pq(response.text)

    # Get the lsd value from the HTML. This is required to make the login request
    lsd = dom('[name="lsd"]').val()

    # Perform the login request
    
    response = session.post('https://www.facebook.com/login.php?login_attempt=1', data={
        'lsd': lsd,
        'email': username,
        'pass': password,
        'default_persistent': '0',
        'timezone': '-60',
        'lgndim': '',
        'lgnrnd': '',
        'lgnjs': '',
        'locale':'en_GB',
        'qsstamp': ''
    })
    
    try:
        uid = session.cookies['c_user']
        dtsg = re.search(r'(type="hidden" name="fb_dtsg" value="([0-9a-zA-Z-_:]+)")', response.text).group(1)

        dtsg = dtsg[dtsg.find("value")+6:]
        dtsg = dtsg[1:-1]
       
        while True:
            print('봇 동작')
            site=session.get('페북주소입력')
            soup = bs(site.text,'html.parser')
            fine_data = soup.find_all('a').text
                          
            #텔레그램봇에게 메세지 전송.
            bot.sendMessage(chat_id=chat_id, text='테스트 중')
            bot.sendMessage(chat_id=chat_id, text=a)
            time.sleep(10000)
       
    except KeyError:
        raise Exception('Login Failed!')

    return uid, dtsg

try:
    main(username='페이스북 아이디 입력', password='페이스북 비번 입력')
except Exception as e:
    logging.exception(e)
    print (e)
```
