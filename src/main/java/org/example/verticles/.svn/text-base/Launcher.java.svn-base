package org.example.verticles;

import org.vertx.java.core.Handler;
import org.vertx.java.core.eventbus.Message;
import org.vertx.java.core.json.JsonArray;
import org.vertx.java.core.json.JsonObject;
import org.vertx.java.deploy.Verticle;

/**
 * Created with IntelliJ IDEA.
 * User: ben
 * Date: 30/08/12
 * Time: 11:41 PM
 * To change this template use File | Settings | File Templates.
 */
public class Launcher extends Verticle {
    @Override
    public void start() throws Exception {
        JsonObject config = container.getConfig();

        JsonObject webServerConfig = new JsonObject();
        webServerConfig.putString("web_root", config.getString("web_root", "target/classes/"));
        webServerConfig.putNumber("port", config.getNumber("port", 8091));
        webServerConfig.putString("host", config.getString("host", "localhost"));

        webServerConfig.putBoolean("bridge", true);
        webServerConfig.putObject("sjs_config", new JsonObject().putString("prefix", "/bus"));

        // One inbound permission
        webServerConfig.putArray("inbound_permitted", new JsonArray().add(new JsonObject().putString("address", "admin.update")));
        // No outbound permissions
        webServerConfig.putArray("outbound_permitted", new JsonArray());

        container.deployModule("vertx.web-server-v1.0", webServerConfig, 20);

        // Send a message to the topic every quarter second
        vertx.setPeriodic(250, new Handler<Long>() {
            private int count = 0;
            @Override
            public void handle(Long aLong) {
                vertx.eventBus().send("admin.update", new JsonObject("{\"msg\":\""+(count++)+"\"}"));
            }
        });

        // Print out all the messages that this server receives
        vertx.eventBus().registerHandler("admin.update", new Handler<Message>() {
            @Override
            public void handle(Message message) {
                System.out.println(message.body);
            }
        });

        System.out.println("Server is running...");
    }
}
