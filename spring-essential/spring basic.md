취준생때 열심히 공부했던개념이지만, 다시한번 정리를 해본다.

\# 객체지향과 스프링

# 회원 도메인 설계

회원 도메인 요구사항  
회원을 가입하고 조회할 수 있다. 회원은 일반과 VIP 두 가지 등급이 있다. 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)

## 회원 도메인 설계의 문제점

이 코드의 설계상 문제점은 무엇일까요? 다른 저장소로 변경할 때 OCP 원칙을 잘 준수할까요?  
DIP를 잘 지키고 있을까요?  
의존관계가 인터페이스 뿐만 아니라 구현까지 모두 의존하는 문제점이 있음

//////////////////  
주문까지 만들고나서 문제점과 해결 방안을 설명  
주문과 할인 도메인 설계 주문과 할인 정책 회원은 상품을 주문할 수 있다.  
회원 등급에 따라 할인 정책을 적용할 수 있다.

# 주문과 할인 도메인 설계

주문과 할인 정책 회원은 상품을 주문할 수 있다.  
회원 등급에 따라 할인 정책을 적용할 수 있다.  
할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용해달라.  
(나중에 변경 될 수 있다.) 할인 정책은 변경 가능성이 높다.  
회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루 고 싶다.  
최악의 경우 할인을 적용하지 않을 수 도 있다. (미확정)

!\[\[Pasted image 20241006172520.png\]\]

!\[\[Pasted image 20241006172619.png\]\]

-   역할과 구현을 분리해서 자유롭게 구현 객체를 조립
-   회원저장소는 물론이고, 할인정책도 유연하게 변경

!\[\[Pasted image 20241006172711.png\]\]

!\[\[Pasted image 20241006172725.png\]\]  
회원을 메모리에서 조회  
정액 할인 정책(고정금액)을 지원해도 주문서비스 변경 x  
역할들의 협력 관계를 그대로 재사용

!\[\[Pasted image 20241006172827.png\]\]  
회원을 메모리가 아닌 실제 DB에서 조회하고, 정률 할인 정책(주문 금액에 따라 % 할인)을 지원해도 주문 서비스를 변경하지 않아도 된다. 협력 관계를 그대로 재사용

# 주문과 할인 도메인 개발

```
package hello.core.discount; 
import hello.core.member.Member;
public interface DiscountPolicy {
/** * @return 할인 대상 금액 */
int discount(Member member, int price);
}
```

# 정액 할인 정책 구현체

```
package hello.core.discount; 
import hello.core.member.Grade; 
import hello.core.member.Member; 
public class FixDiscountPolicy implements DiscountPolicy { private int discountFixAmount = 1000; //1000원 할인 
                                                          @Override                                                public int discount(Member member, int price) { 
                                                          if (member.getGrade() == Grade.VIP) { 
                                                          return discountFixAmount; } else { return 0;
                                                               } 
    }
 }
```

# 비즈니스요구사항과 설계

-   회원은 상품을 주문할 수 있다
-   회원 등급에 따라 할인 정책을 적용할 수 잇다
-   할인 정책은 모든 VIP는 1000원을 할인해주는 고정금액할인을 적용
-   할인정책은 오픈직전까지 고민을 미루고 싶다

순수한 자바로만 진행

회원도메인설계  
클라이언트 - >회원서비스 ->( 회원가입 회원조회)- 회원저장소 (인터페이스)

회원클래스 다이어그램  
memberservice - memberserviceimpl  
memberrepository <- memorymemberrepository - dbrepository

회원 객체 다이어그램  
클라이언트 - 회원서비스 - 메모리 회원저장소

회원서비스 - memberserviceimpl

```
public interface MemberRepository{
    void save(Member member);
    Member findById(Long memberId);
}

```

\`

```
public class MemoryMemberRepository implements MemberRepository
    private static Map<Long, Member> store = new HashMap<>();

@Override 
public void save(Member member)
    store.put(member.get(), member);
}

@Override 
public void save(Member member)
    store.put(member.get(), member);
}

```

```
public interface memberService{
    void join(Member member);
    Member findMember(Long memberId);
}
```

```

public class MemberServiceImpl implemnts MemberService{
    private final MemberRepository memberRepository = new MemoryMemberRepository();

}
```

```

public class MemberApp{
    public static void main(String[] args){
    MemberService memberService = new MemberServiceImpl();
    Member member = new Member(1l, "member", Grade.VIP);
memberService.join(member);

    }

}

```

```

public class MemberServiceTest{

    memberService memberService = new MemberServiceImpl();

@Test
void join(){
//when


    }
//then


Assertions.assertThat(member).isEqualTo(findMember);

}

```

회원 도메인 설계의 문제점

-   이코드의 설계상 문제점은
-   ocp원칙을 잘준수하는가
-   dip를 잘지키고 있을까 ?
-   의존관계가 인터페이스 뿐만아니라 구현까지 모두 의존 하는 문제점이 있음

```
public class FixDiscountPolicy implements DiscountPolicy{

    private int discountFixAmount = 1000; // 1000원할인

@Override
public int discount(Member member, int price){
    if(member.getGrade() == Grade.Vip)
    {return discountlFixAmount;
    }else{
return  0;
        }
    }
}
```

```


public class Order{

    private Long memberId;
    .
    .


public int calculatePrice(){
    return itemPrice - discountPrice;
    }

}
```

```

public interface OrderService{
    Order createOrder(Long memebrid, String itemName, int itemPrice);


}
```

```

public class OrderServiceimpl implements OrderService{
    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();

@Override 
public Order createOrder (Long memberid, String ItemName, int itemPrice){
Member member = memberRepository.findById(memberId);
int discountPrice = discountPolicy.dicount(member, itemPrice);



return new Order(memberId, itemName, itemPrice, discountPrice);
        }
    }
}
```

# 주문과 할인 도메인 실행과 테스트

.  
.  
.

정리  
역할과 구현을 분리 해서 자유롭게 구현 객체를 조립할 수 잇게 설계 덕분에 회원저장소는 물론이고 할인 정책도 유연하게 변경할 수 있다

# 새로운 할인정책의 문제점

할인정책을 변경하려면 클라이언트인 OrderServiceImpl 코드를 고쳐야한다

```

private final DiscountPolicy discountPolicy = new RateDiscountPolicy()
;
```

문제점 발견  
우리는 역할과 구현을 충실하게 분리했다  
다형성도 활용하고 인터페이스와 구현객체를 분리했다  
OCPDIP같은 객체 지향 설계 원칙을 충실히 준수햇다  
\-> 사실이 아니다

dip 주문 클라이언트 는 dip를 지킨 것 같은데?

-   클래스 의존관계를 분석 구체 (구현)클래스에도 의존
-   추상(인터페이스)의존 discountpolicy
-   구체 클래스 fixdiscountpolicy, ratediscountpolicy
    -   ocp 변경하지 않고 확장할 수 잇다고 했는데
    -   지금 코드는 확장해서 변경하면 클라이언트 코드에 영향을 준다 ocp를 위반

FixDiscountPolicy를 RateDiscountPolicy로 변경하는 순간 OrderServiceImpl의 소스 코드도 함께 변경해야한다 ocp위반

어떻게 문제를 해결할 수 있을까?  
클라이언트 코드인 orderserviceimpl은 discountpolicy의 인터페이스 뿐만아니라 구체클래스도 함께 의존한다  
그래서 구체 클래스를 변경할때도 클라이언트 코드도 함께 변경해야한다  
dip위반 추상에만 의존하도록 변경(인터페이스에만 의존)  
dip를 위반하지 않도록 인터페이스에만 의존하도록 의존관계를 변경하면된다.

인터페이스에만 의존하도록 설계를 변경하자  
orderserviceimpl -> discountpolicy (fix discount policy ratediscountpolicy)

인터페이스에만 의존하도록 설계와 코드 변경  
NPE가 납니당

이문제를 해결하려면 누군가가 클라이언트의 orderserviceimpl에 discountpolicy의 구현 객체를 대신 생성하고 주입해줘야한다

# 관심사의 분리

Appconfig등장  
애플리케이션의 전체동작 방식을 구성하기 위해 구현객체를 생성하고 연결하는 책임을 가지는 별도의 클래스를 만들자

appconfig

```


public class Appconfig{
    public MemberService memberService(){
        return new MemberServiceImpl (new MemoryMemberRepository());

        }


public OrderService orderService(){
    return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
}

}

```

```
public class OrderServiceImpl implement OrderService{

private final DiscountPolicy discountPolicy;
private final MemberRepository memberRepository;

public OrderServicpeImpl... 생성자

//dip원칙을 잘지키고 있다

} 
```

-   appconfig는 애플리케이션의 실제동작에 필요한 구현 객체를 생성
-   memberserviceimpl memorymemberrepository orderserviceimpl fixdiscountpolicy
-   appconfig는 생성한 인스턴스의 참조를 생성자를 통해서 주입 연결해준다
-   memberseviceimpl -> memorymemberrepository

설계 변경으로 memebrserviceimpl은 memorymemberrepository를 의존하지 않는다 단지 memeberrepositiory 인터페이스만 의존한다  
memberserviceimpl입장에서 생성자를 통해 어떤 구현 객체가 들어올지는 알 수 없다  
memberserviceimpl생성자를 통해 어떤 구현 객체를 주입할지는 오직 외부에서 정한다  
memberseviceimpl은 이제부터 의존관계에대한 고민은 외부에서 맡기고 실행에만 집중한다

dip 완성 memberserviceimpl은 추상에만 의존하면 된다  
관심사의 분리 : 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리

memberServiceimpl <- 생성+ 주입 memoryMemberRepository x001 -> 생성 memorymemberrepository

appconfig 객체는 memorymemberrepository객체를 생성하고 그참조값을 memberserviceimpl을 생성하면서 생성자로 전달한다

클라이언트인 memberServiceimpl입장에서 보면 의존관계를 마치 외부에서 주입해주는 것과 같다고 해서 di 의존관계 주입이라고 한다

```
//생성자 주입

public class OrderApp{
    public static void main(String[] args){

    Appconfig appConfig = new AppConfig();
    MemberService memberService = appConfig.memberService();

    }


}


```

orderserviceimp은 fixdisountpolicy에 의존하지 않는다  
orderserviceimp생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부에서 결정

appconfig는 구체클래스를 선택한다 배역에 맞는 담당배우를 선택 애플리케이션이 어떻게 동작해야할지 전체구성을 책임진다

이제 각 배우들은 담당 기능을 실행하는 책임만 지면된다  
orderserviceimpl은 기능을 실행하는 책임만 진다

AppConfig 리팩터링  
appconfig를 보면 중복이 있고, 역할에 따른 구현이 잘 안보인다

new MemoryMemberRepository()이부분이 중복 제거되었다

appconfig에서 할인정책 역할을 담당하는 구현을 fixdiscountpolicy -> ratediscountpolicy객체로 변경  
할인정책을 변경해도 appconfig만 변경하면된다  
orderserviceimpl을 포함해서 사용영역의 어떤 코드도 변경할필요가 없다

# solid

srp : single resposibility principle  
ocp: open-close principle  
lsp: liskov substitution principle  
isp: interfase segregation principle  
dip : dependency inversion principle

SRP:단일 책임 원칙

세모 별 책임을 가질때 = 별/ 세모 원칙을 갖게

-   하나의 클래스는 단 한가지의 변경 이유만을 가져야한다.

변경 이유 = 책임

-   객체가 가진 공개 메서드, 필드, 상수 등은 해당 객체의 단일 책임에 의해서만 변경되는가?
-   관심사의 분리
-   높은 응집도, 낮은 결합도

---

'책임'을 볼 줄 아는 눈이 필요하다

```
public static void main(String[] args){
    showGameStartComments();
    initializeGame();

}
```

게임을 실행하는 main 과  
// 지뢰찾기를 실행하는 클래스를 별도로 두면 어떨까?

```
    public class Minesweeper{

    }
//모든 함수를 위 클래스에 둘것이다
```

```

public static void main(String[] args){
    Minesweeper minesweeper = new Minesweeper();
    minesweeper.run();
}
```

\-> static 없애주기

입력 핸들러 분리

```


private final ConsoleInputHandler consoleInputHandler = new CosoleInputHandler();


private final ConsoleOutputHandler consoleOutputHandler = new CosoleOutputHandler();


```

```

public class ConsoleOutPutHandler{
    void showGameStartComments(){
    sout..
    sout..
    sout...
    }
}
```

```
public void run(){
    consoleOutputHandler.showGameStartComments();
    initializeGame();
}
    while(true){
        try{
            consoleOutputHandler.showBoard(BOARD);

        if(doesUserWinTheGame()){

        }

        if(doesIserLoseTheGame()){            consoleOutputHandler.printGameLosingComment();
            break;
            }
    String cellInput = getCellInputFromUser();
    String userAcctionInput = getUserActionInputFromUser();
    actInCell(cellInput,userActionInput);
    }catch (AppException e){
        consoleOutputHandler.printExceptionMessage(e);
//consoleOutputHandler.printExceptionMessage(e.getMessage());
}

    }

    }
}
```

```

public class GameBoard{

    private final Cell[][] board = new Cell[BOARD_ROW_SIZE][BOARD_COL_SIZE];

    public GameBoard(int rowSize, intk colSize){

    board = new Cell[rowSize][colSize]

    }

}
```
