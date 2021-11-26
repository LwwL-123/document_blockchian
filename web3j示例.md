# web3j示例

```java
import org.web3j.crypto.CipherException;
import org.web3j.crypto.Credentials;
import org.web3j.crypto.WalletUtils;
import org.web3j.protocol.Web3j;
import org.web3j.protocol.core.DefaultBlockParameterName;
import org.web3j.protocol.core.methods.response.*;
import org.web3j.protocol.http.HttpService;
import org.web3j.tx.Contract;
import org.web3j.tx.Transfer;
import org.web3j.utils.Convert;
import java.io.File;
import java.io.IOException;
import java.math.BigDecimal;
import java.math.BigInteger;
import java.security.InvalidAlgorithmParameterException;
import java.security.NoSuchAlgorithmException;
import java.security.NoSuchProviderException;
import java.util.List;

public class eth {

    public static void main(String[] args) throws Exception {

        Web3j web3 = Web3j.build(new HttpService("http://localhost:9933/"));

        //创建新账户
        creatNew();
        //加载账户
        Credentials credentials = loadWallet();

        //转账
        transfer(web3,credentials);

        //打印一些数值
        print(web3);

        //合约1
        contract1(web3,credentials);

        //合约2
        contract2(web3,credentials);
    }

    private static void creatNew() throws CipherException, InvalidAlgorithmParameterException, NoSuchAlgorithmException, NoSuchProviderException, IOException {
        String pwd = "lzw980618";
        String walletFileName0 = "";
        String walletFilePath0 = "/Users/lzw/Solidity";
        //创建新账户
        walletFileName0 = WalletUtils.generateFullNewWalletFile(pwd, new File(walletFilePath0));
    }


    //加载钱包
    private static Credentials loadWallet() throws IOException, CipherException {
        String walleFilePath = "/Users/lzw/Solidity/UTC--2021-07-05T06-31-28.205000000Z--0085cc43458e06d29fe607d2dca982060a5e1fa3.json";
        String passWord = "lzw980618";
        Credentials credentials = WalletUtils.loadCredentials(passWord, walleFilePath);
        String address = credentials.getAddress();
        BigInteger publicKey = credentials.getEcKeyPair().getPublicKey();
        BigInteger privateKey = credentials.getEcKeyPair().getPrivateKey();

//        System.out.println("address=" + address);
//        System.out.println("public key=" + publicKey);
//        System.out.println("private key=" + privateKey);
        return credentials;
    }

    //转账
    private static void transfer(Web3j web3, Credentials credentials) throws Exception {
        if (web3 == null) return;
        if (credentials == null) return;

        String address_to = "0x4225aB2c1F0A6753C0B03721D01e848445942464";
        TransactionReceipt send = Transfer.sendFunds(web3, credentials, address_to, BigDecimal.ONE, Convert.Unit.FINNEY).send();

        System.out.println("Transaction complete:");
        System.out.println("trans hash=" + send.getTransactionHash());
        System.out.println("from :" + send.getFrom());
        System.out.println("to:" + send.getTo());
        System.out.println("gas used=" + send.getGasUsed());
        System.out.println("status: " + send.getStatus());
    }


    private static void print(Web3j web3) throws IOException {
        Web3ClientVersion web3ClientVersion = web3.web3ClientVersion().send();
        //客户端信息
        String clientVersion = web3ClientVersion.getWeb3ClientVersion();
        //块高
        BigInteger blockNumber = web3.ethBlockNumber().send().getBlockNumber();
        //余额
        BigInteger ethGetBalanceRequest = web3.ethGetBalance("0x341463dFFC75970AFd547a951bd45188b8979995", DefaultBlockParameterName.LATEST).send().getBalance();
        //eth账户
        List<String> ethAccountsRequest = web3.ethAccounts().send().getAccounts();

        System.out.println("客户端信息："+clientVersion);
        System.out.println("当前块高"+blockNumber);
        System.out.println("账户余额"+ethGetBalanceRequest);
        System.out.println("当前所有账户"+ethAccountsRequest);
    }

    private static void contract1(Web3j web3,Credentials credentials) throws Exception {
        //新创建一个合约
        SimpleStorage simpleStorage = SimpleStorage.deploy(web3, credentials, Contract.GAS_PRICE, Contract.GAS_LIMIT).send();
        simpleStorage.set(new BigInteger("1000")).send();
        System.out.println(simpleStorage.get().send());
    }

    private static void contract2(Web3j web3,Credentials credentials) throws Exception {
        //已有合约地址，用load
        PayableTest payableTest = PayableTest.load("0x370a4408533f62881330be5a748c3512a82523fc",web3, credentials, Contract.GAS_PRICE, Contract.GAS_LIMIT);
        System.out.println(payableTest.isValid());

        System.out.println(payableTest.getThis().send());
        BigInteger bigInteger = new BigInteger("6000000000000000");

        payableTest.transfer("0x4225aB2c1F0A6753C0B03721D01e848445942464", bigInteger).send();

        System.out.println(payableTest.getrandBalance("0x4225aB2c1F0A6753C0B03721D01e848445942464").send());
    }


}
```