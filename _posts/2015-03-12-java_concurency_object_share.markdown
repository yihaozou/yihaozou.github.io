---
author: 邹政华
comments: true
date: 2015-3-12
published: false 
tags: Java
layout: post
title:  Java并发编程实践－Executor框架
categories: 读书 
---


线程池简化了线程的管理工作，java.util.concurrent提供了一种灵活的线程池实现作为Executor框架的一部分.在Java类库中, 任务执行的主要抽象不是Thread, 而是Executor。

public interface Executors{
	void execute(Runnable command);
}

Executor基于生产者－消费者模式，提交任务的操作相当于生产者，执行任务的线程则相当于消费者。如果要在程序中实现一个生产者－消费者的设计，那么最简单的方式通常就是使用Executor。

	class taskExecutionWebServer{
		private static final int NTHREADS = 100;
		private static final Executor exec =  Executors.newFixedThreadPool(NTHREADS);

		public static void main(String[] args) throws IOException{
			ServerSocket socket = new ServerSocket(80);
			while(true){
				final Socket connection = socket.accept();
				Runnable task = new Runnable(){
					public void run(){
						handleRequest(connection);
					}
				};
				exec.execute(task);
			}
		}
	}

在TaskExecutionWebServer中，通过使用Executor，将请求处理任务的提交与任务实际执行解耦开来，并且只需采用另一种Executor实现，就可以改变服务器的行为。改变Executor实现或配置所带来的影响要远远小于改变任务提交方式带来的影响。

在线程池中执行任务比单独为每个任务分配一个线程优势更多。首先可分摊线程创建和销毁过程中产生的巨大开销，提高响应性能。同时通过适当调整线程池的大小，提高处理器利用效率；还能防止过多线程耗尽系统资源。

Executor的集中静态工厂方法：newFixedThreadPool, newCachedThreadPool, newSingleThreadExecutor, newScheduledThreadPool.



	class taskExecutionWebServer{
		private static final int NTHREADS = 100;
		private static final Executor exec =  Executors.newFixedThreadPool(NTHREADS);

		public static void main(String[] args) throws IOException{
			ServerSocket socket = new ServerSocket(80);
			while(！exec.isShutdown()){
				try{
					final Socket connection = socket.accept();
					Runnable task = new Runnable(){
						public void run(){
							handleRequest(connection);
						}
					};
					exec.execute(task);
				}catch(RejectedExecutionException e){
					if(!exec.isShutdown())
						log("task submission rejected", e);
				}
				
			}
		}
		public void stop(){
			exec.shutdown();
		}

		void handleRequest(Socket connection){
			Request req = readRequest(connection);
			if(isShutdownRequest(req))
				stop();
			else
				dispatchRequest(req);
		}
	}


ExecutorService是java.util.concurrent包提供的接口，在Executor的基础上提供生命周期管理。ExecutorService的生命周期有三种：运行，关闭，和已终止。其中shutdown方法将执行平缓的关闭过程，不再接受新的任务，同时等待已经执行完毕的任务执行完毕。

在单个客户请求中仍有可能存在可发掘的并行性，下面给出一个页面浏览器的例子。

最简单的方式是对html文档进行串行处理。当遇到文本标签是，将其绘制到页面中。当遇到图像缓存时，先通过网络获取它，然后再将起绘制到图像缓存中。图像下载过程中的大部分时间都是再等待I/O操作执行完成，在这期间CPU几乎不做任何工作，因此这种串行执行的方法没有充分地利用CPU，使得用户再看到最终页面之前要等待过长的时间。

	public class SingleThreadRenderer{
		void renderPage(CharSequence source){
			renderText(source);
			List<ImageData> imageData = new ArrayList<ImageData>();
			for(ImageInfo imageInfo: scanForImageInfo(source))
				imageData.add(imageInfo.downloadImage());
			for(ImageData data: imageData)
				renderImage(data);
		}
	}

通过将问题分解为多个独立的任务并发执行，能够获得更高的CPU利用率和响应灵敏度。

Runnable是一种有很大局限的抽象，虽然run能写入到日志文件或者将结果放入某个共享的数据结构，但它不能返回或抛出一个受检查的异常。

Callable是一种更好的抽象:它认为主入口点（即call）将返回一个值，并可能抛出一个异常。再Executor中包含了一些辅助方法能将其他类型的任务封装为一个Callable，，例如Runnable。

再Executor框架中，已提交但尚未开始的任务可以取消，但对于那些已经开始执行的任务，只有当它们能响应中断时，才能取消，取消一个已经完成的任务不会有任何影响。

Callable和Future有助于标示协同任务之间的交互。再FutureRenderer中，我们创建了一个Callable来下载所有的图像，并将其提交到一个ExecutorService。这将返回一个描述任务执行情况的Future。当祝任务需要图像时，它会等待Furure.get的调用结果，这样，图像的下载任务和渲染页面是同步进行的。
public class FutureRenderer{
	private final ExecutorService executor= ...;

	void renderPage(CharSequence source){
		final List<ImageInfo> imageInfos = scanForImageInfo(source);
		Callable<List<ImageData> task = new Callable<List<ImageData>>(){
			public List<ImageData> call(){
				List<ImageData> result = new ArrayList<ImageData>();
				for(ImageInfo imageInfo:ImageInfos)
					result.add(ImageInfo.downloadImage());
				return result;
			}
		};

		Future<List<ImageData>> future = executor.submit(task);
		renderText(source);

		try{
			List<ImageData> imageData = future.get();
			for(ImageData data:imageData)
				renderImage(Data);
		}catch(InterruptedException e){
			Thread.currentThread().interrupt();
			future.cancel(true);
		}catch(ExecutionException e){
			throw launderThrowable(e.getCause());
		}
	}
}

如果渲染文本的速度远远高于下载图像的速度，那么程序的性能与串行执行时的性能差别不大，因此程序还有优化的空间。

只有当大量相互独立且同构的任务可以并发进行处理时，才能体现出将程序的工作负载分配到多个任务中带来的真正性能提升。

CompletionService将Executor和BlockingQueue的功能融合在一起，可以将Callable交给它来执行，然后使用类似于队列操作的take和poll方法来获得已完成的结果，而这些结果会在完成时被封装为Future。


public class Renderer{
	private final ExecutorService executor;

	Renderer(ExecutorService executor){
		this.executor = executor;
	}

	void renderPage(CharSequence source){
		final List<ImageInfo> imageInfos = scanForImageInfo(source);
		CompletionService<ImageData> completionService = new ExecutorCompletionService<ImageData>(executor);
		for(final ImageInfo imageInfo:info){
			completionService.submit(new Callable<ImageData>(){
				public ImageData call(){
					return imageInfo.downloadImage();
				}
			}
		}

		renderText(source);

		try{
			for(int t=0,n=info.size(); t<n; t++){
				Future<ImageData> f = completionService.take();
				ImageData imageData = f.get();
				renderImage(imageData);
			}
		}catch(InterruptedException e){
			Thread.currentThread().interrupt();
		}catch(ExecutionException e){
			throw launderThrowable(e.getCause());
		}
	}
}



