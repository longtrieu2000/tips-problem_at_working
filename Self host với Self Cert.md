1 vài vấn đề khi bạn dùng cert tự làm

Giả sự bạn tự tạo cert ssl với certbot, openssl,... và bạn import vào nginx để tự redirect http sang https thì sẽ có 1 số vấn đề bạn cần phải để ý như sau:
  - Đầu tiên phải hiểu là nếu bạn đã có domain sẵn có và 1 DNS server có sẵn để host thì việc đơn giản là bạn chỉ cần cấu hình như bình thường và server DNS sẽ tự phân giải
  - Nếu bạn không có DNS server sẵn thì chỉ cần dựng hoặc dùng có sẵn như AD, hoặc thêm host vào /etc/hosts và nginx làm công việc reverse proxy
  - Lúc này việc đầu tiên cần để ý là các client/host phải phân giải được domain, với container/docker ví dụ như confluence, jira việc đầu tiên là phải chung dải mạng với nginx. Sau đó thêm host vào các container để nó phân giải domain rồi có thể đi qua nginx reverse proxy. Nhưng lúc này ta vẫn chưa thể thấy được https://domain.com vì các self cert không được trust.
  - Có 2 vấn để về trust self cert: ở OS dùng CA (vd: /usr/local/share/ca-cert/...) nên nếu muốn trust cert ở os thì ta cần import cert vào và update-ca-cert. Nhưng confluence và jira chạy JAVA nên cần phải trust cert ở Java keystore thì 2 ứng dụng này mới thấy được domain
    
         (vd:keytool -importcert -alias reverseproxy -file /usr/local/share/ca-certificates/reverseproxy.crt \
                  -keystore /usr/local/openjdk-11/lib/security/cacerts \
                  -storepass changeit -noprompt )
