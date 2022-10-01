---
author: "Hoàng Kiên"
title: "Spring boot - Annotation Transaction"
date: 2022-09-28
description: "Transaction khi sử dụng trong springboot-hibernate"
tags: ["Transaction", "springboot"]
thumbnail: /image001.jpg
---

<h3>Mở đầu:</h3>

 Lời đầu : Hiện tại đối với anh em sử dụng java thì Spring framework là một trong những nền tảng phổ biến nhất 
 để có thể xây dựng các chương trình server-backend đáp ứng nhu cầu phát triển nhanh cũng như ứng dụng linh hoạt . Khi phát triển server-backend thì nhu cầu kết nối quản lý đến database cũng là một nhu cầu không thể thiếu được khi xây dựng một server-backend . Vậy hôm nay mình muốn chia sẻ cách thức để một chương trình server-springboot sử dụng để "liên lạc" với database thông qua jpa - hibernate theo một cách flow-to-flow .

 Mục đích : Đưa ra ý tưởng cụ thể về cách thức hoạt động của spring-jpa dựa trên những concept về datasource , connection pool , spring aop proxy ,  đặc biệt @transaction anotation ( commit , rollback) theo flow-to-flow 

<h3>Ý tưởng spring thực hiện :</h3>
 Ý tưởng để có thể thực hiện được jpa-hibernate trên springboot:

- B1:  Ta có khái niệm Connection pool va dataSource ( Connection pool sẽ lấy các connection cần  thiết từ dataSource và quản lý nó trong pool ) . Mỗi khi có yêu cầu sử dụng connection ( vd : khi call api lấy ra thông tin  ) thì sẽ được cấp phát từ Connection pool.

{{< highlight java >}}

@Configuration
public class DataSourceConfiguration extends HikariConfig {
    private static HikariDataSource dataSource;

    @Bean("dataSource")
    @ConfigurationProperties("spring.datasource.hikari.mariadb")
    public static HikariDataSource dataSource() {
        dataSource = DataSourceBuilder.create().type(HikariDataSource.class).build();
        return dataSource;
    }

}


{{< /highlight >}}


B2: Ta có khái niệm sử dụng @Transactional . (tương đương việc bắt đầu một transaction trong database và lấy ra 1 connection từ connection pool) 

{{< highlight java >}}

//ví dụ 
    @Autowired
    LoyaltyRepo loyaltyRepo;

@Transactional
    public ResponseThanhToan thanhToan(RequestThanhToan req) throws Exception {
        ///.. các logic phải thực hiện gồm java code xử lý và CRUD trong database
        // exp: loyaltyRepo.find()
        }

{{< /highlight >}}
Vậy @Transactional hoạt động như nào khi đặt trên 1 menthod?

Step1 : Khi khởi động spring boot sử dụng java reflection để phát hiện ra các component bean chúng ta khai báo và load chúng vào bean container


Step2: Khi khởi tạo component bean chứa  @Transactional trên menthod -> spring boot biết được đây là class có chứa 1 menthod sử dụng @Transactional -> spring boot sử dụng Spring AOP chèn thêm các hàm xử lý vào trước và sau khi gọi đến menthod thực sự .


Note: Spring AOP được biết đến trong Core Technologies của spring framework được hình thành trên nền tảng java reflect proxy ( JDK dynamic proxy) or a CGLIB proxy 


https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/aop.html#aop-understanding-aop-proxies .


Ta sẽ có thể tưởng tượng như này spring aop sẽ chèn các logic đi kèm với businessLogic(). Trong đó businessLogic() chính là menthod chúng ta đặt @Transactional:

{{< highlight java >}}
UserTransaction utx = entityManager.getTransaction(); 

try { 
    utx.begin(); 
    businessLogic();
    utx.commit(); 
} catch(Exception ex) { 
    utx.rollback(); 
    throw ex; 
} 
{{< /highlight >}}


Như vậy trước khi ta call tới menthod thì việc xử lý lấy connection từ connection pool đã được spring aop thực hiện. 
Và sau khi xử lý thì việc commit transaction và roll back cũng đã được gọi sau khi menthod chính được gọi.


B4: Khi ta dùng Repository để CURD  database bên trong bản chất là sử dụng connection lấy ra từ @Transactional và thực hiện statement sql.

Ta có thể tạo custom repository như sau:

{{< highlight java >}}

@Repository
public class LoyatyRepoCusImpl implements LoyatyRepoCus {

    @PersistenceContext
    private EntityManager entityManager;

    @Override
    @Transactional
    public void getPessimisticLockRow(RequestThanhToan req) throws Exception {
        StringBuilder sqlBuilder = new StringBuilder();
        sqlBuilder.append(" SELECT *  FROM LOYALTY WHERE id = :id FOR UPDATE NOWAIT ");
        Query query = entityManager.createNativeQuery(sqlBuilder.toString());
        query.setParameter("id",req.getId().toString());
        NativeQueryImpl nativeQuery = (NativeQueryImpl) query;
        nativeQuery.getResultList();
    }
}

{{< /highlight >}}

<h3>Phần demo :</h3>


 Để có một cách thức tiếp cận vấn đề theo flow  mình đưa ra bài toán giả định: Phát triển ứng dụng quản lý điểm loyalty. Thực hiện xây dựng 1 restApi để có thể thanh toán cho điểm loyalty cho một khách hàng dựa trên số điểm khách hàng có .


 Có 2 kịch bản đưa ra : 
 + Kịch bản 1  bút toán thành công:
    - hệ thống nhận yêu cầu thanh toán điểm -> Ghi nhận đang bắt đầu thanh toán cho KH -> check điểm dư khả dụng khách hàng thành công-> lưu yêu cầu thanh toán -> trừ điểm khách hàng -> commit transaction database ->  trả về thành công 
 + Kịch bản 2 bút toán thất bại:
    - hệ thống nhận yêu cầu thanh toán điểm -> Ghi nhận đang bắt đầu thanh toán cho KH -> check điểm dư khả dụng khách hàng thất bại -> throw exception -> rollback transaction database lại toàn bộ bút toán đã được thực hiện trong database của transaction này.

<h4>Chuẩn bị :</h4>

Xây dựng project cho bài toán giả định :

DataBase:

bảng LOYALTY chứa thông tin live Số điểm KH

bảng HIS_LOYALTY chứa thông tin mỗi lần thanh toán

{{< highlight sql >}}
CREATE TABLE ebanking_theme.LOYALTY (
                                                       ID VARCHAR(40) PRIMARY key not null,
                                                       SO_DIEM_LOYALTY int DEFAULT 0,
                                                       TRANG_THAI_TAI_KHOAN VARCHAR(40) ,
                                                       UPDATED_DATE datetime                                                   
);

CREATE TABLE ebanking_theme.HIS_LOYALTY (
                                                       ID VARCHAR(40) PRIMARY key not null,
                                                       NOI_DUNG VARCHAR(500) ,
                                                       UPDATED_DATE datetime                                                   
);
{{< /highlight >}}

B1: xây dựng controller

{{< highlight java >}}


@RestController
@RequestMapping(value = { "/loyalty-ms" })
public class LoyaltyController  {

    @Autowired
    LoyaltyService loyaltyService;

    @RequestMapping(value = "/loyalty-thanhtoan", method = RequestMethod.POST, produces = "application/json")
    public ResponseThanhToan loyalty(@RequestBody RequestThanhToan req) throws Exception {
        ResponseThanhToan res = loyaltyService.thanhToan(req);
        return res;
    }

    @RequestMapping(value = "/get-list-loyalty", method = RequestMethod.POST, produces = "application/json")
    public List<Loyalty> list(@RequestBody RequestThanhToan req) throws Exception {
        List<Loyalty> res = loyaltyService.list(req);
        return res;
    }

}

{{< /highlight >}}


B2: xây dựng tầng service nơi thực hiện bút toán nơi quyết định commit hay rollback transaction

{{< highlight java >}}

   
@Service
public class LoyaltyService {

    @Autowired
    LoyaltyRepo loyaltyRepo;

    @Autowired
    LoyaltyHisRepo loyaltyHisRepo;

    @Transactional(rollbackOn = Exception.class)
    public ResponseThanhToan thanhToan(RequestThanhToan req) throws Exception {

        // bảo đảm cho việc khi update 1 bản ghi quan trọng ta sử dụng phương pháp khóa bi quan(Pessimistic Locking )
        // để cho không thằng connection nào có thể thao tác update , delete bản ghi trong tg server thanh toan xu ly
        // SELECT *  FROM LOYALTY WHERE id = '01b293a0-c290-4ee5-96ff-40baafb2897a' FOR UPDATE
        loyaltyRepo.getPessimisticLockRow(req);

        HisLoyalty his = new HisLoyalty();
        his.setNoiDung(req.getId().toString()+ " bat dau thuc hien thanh toan");
        his.setUpdatedDate(new Date());
        loyaltyHisRepo.save(his);
        loyaltyHisRepo.flush();

        //tim kiem thông tin loyalty tương ứng
        Loyalty loyalty = loyaltyRepo.findById(req.getId()).orElse(null);
        long sodiemHienTai = loyalty.getSoDiemLoyaty();

        if(sodiemHienTai<req.getSoDiemThanhToan()){
            //throw exception để roll back
            throw new Exception("So diem du khong hop le");
        }else{

            // thực hiện lưu lịch sử giao dich
            his.setNoiDung(req.getId().toString()+ " da thuc hien thanh toan:"+req.getSoDiemThanhToan());
            his.setUpdatedDate(new Date());
            loyaltyHisRepo.save(his);
            loyaltyHisRepo.flush();
            // flush() khi cần thiết nó sẽ tự flush khi thực hiện 1 câu lệnh sql mới,
            // hoặc  commit  khi chạy hết function

            //thuc hien tru diem
            long soDiemConlai= sodiemHienTai-req.getSoDiemThanhToan();
            loyalty.setSoDiemLoyaty(soDiemConlai);
            loyaltyRepo.save(loyalty);

            Loyalty loyaltyNow = loyaltyRepo.findById(req.getId()).orElse(null);
            System.out.println("Gia tri moi nhat:"+loyaltyNow);

            return new ResponseThanhToan(req.getId(),"Thanh Cong");
        }

    }

    @Transactional(rollbackOn = Exception.class)
    public List<Loyalty> list(RequestThanhToan req) throws Exception {
        List<Loyalty> list = loyaltyRepo.findAll();
        return list;
    }

}

{{< /highlight >}}

B3: xây dựng repo custom query raw


{{< highlight java >}}

@Repository
public class LoyatyRepoCusImpl implements LoyatyRepoCus {

    @PersistenceContext
    private EntityManager entityManager;

    @Override
    @Transactional
    public void getPessimisticLockRow(RequestThanhToan req) throws Exception {
        StringBuilder sqlBuilder = new StringBuilder();
        sqlBuilder.append(" SELECT *  FROM LOYALTY WHERE id = :id FOR UPDATE NOWAIT ");
        Query query = entityManager.createNativeQuery(sqlBuilder.toString());
        query.setParameter("id",req.getId().toString());
        NativeQueryImpl nativeQuery = (NativeQueryImpl) query;
        nativeQuery.getResultList();
        System.out.println("ok");
    }
}
{{< /highlight >}}

**Demo hình ảnh cho kịch bản thành công :**

(1) hệ thống nhận yêu cầu thanh toán 1 điểm loyalty -> (2)Ghi nhận đang bắt đầu thanh toán cho KH -> (3)check điểm dư khả dụng khách hàng thành công-> (4)lưu yêu cầu thanh toán -> (5)trừ điểm khách hàng -> (6)commit transaction database -> (7) trả về thành công

Hình ảnh**

(2) Ghi nhận đang bắt đầu thanh toán cho KH
![Placeholder](/1.png)
Ghi nhận id: fea98319-5774-4837-a29f-9afbdc1da4af thông tin thanh toán và kiểm tra KH đang có 191 điểm 
![Placeholder](/4.png)
![Placeholder](/2.png)

(6) sau khi commit transactioon update lại bản ghi fea98319-5774-4837-a29f-9afbdc1da4af và trừ diểm KH thành 190

![Placeholder](/2-k.png)
![Placeholder](/3.png)

**Demo hình ảnh cho kịch bản thất bại :**

(1) hệ thống nhận yêu cầu thanh toán điểm -> (2) Ghi nhận đang bắt đầu thanh toán cho KH -> (3) check điểm dư khả dụng khách hàng thất bại -> (4)throw exception rollback transaction database lại toàn bộ bút toán đã được thực hiện trong database của transaction này.

Hình ảnh**

(2) Ghi nhận đang bắt đầu thanh toán cho KH với id : 9c9c653c-8e49-477e-a19b-8d046b68e9f8
![Placeholder](/7.png)

(4)throw exception rollback transaction database .(roolback id: 9c9c653c-8e49-477e-a19b-8d046b68e9f8 )
![Placeholder](/8.png)

![Placeholder](/6.png)

Kết luận : 

1:Khi sử dụng @Transactional phải có mục đích cụ thể vì nó đại diện cho cho việc lấy 1 connection từ connection pool

2:Không phải cứ có @Transactional là sẽ lấy 1 connection từ pool ( với giá trị mặc định @Transactional là propagation = Propagation.REQUIRES sẽ chỉ lấy 1 conenction từ hàm đặt transaction đầu tiên . nếu bên trong có khai báo @Transactional thì sẽ dùng cùng 1 connection  )

Hoàng Kiên 22/09/2022



{{< css.inline >}}

<style>
.canon { background: white; width: 100%; height: auto; }
</style>

{{< /css.inline >}}
