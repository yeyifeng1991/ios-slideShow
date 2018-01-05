# ios-slideShow
ios使用CollectionView制作轮播图
轮播图思路
1.创建UICollectionView UIPageControl
2.创建完UICollectionView 之后创建定时器，并且设置图片控制器的当前页数为0
```
- (void)initialUSerInterface{
    
    UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc]init];
    layout.itemSize = CGSizeMake(self.view.frame.size.width, 250);
    layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
    
    
    UICollectionView *collectionView = [[UICollectionView alloc]initWithFrame:CGRectMake(0, 10, self.view.frame.size.width, 250) collectionViewLayout:layout];
    collectionView.backgroundColor = [UIColor whiteColor];
    collectionView.delegate = self;
    collectionView.dataSource = self;
    [collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:cellIdef_];
    collectionView.pagingEnabled = YES;
    collectionView.showsHorizontalScrollIndicator = NO;
    collectionView.showsVerticalScrollIndicator = NO;
    collectionView.bounces = NO;
    collectionView.contentSize = CGSizeMake(3 * collectionView.bounds.size.width, 0);
    [self.view addSubview:collectionView];
    
    self.collectionView = collectionView;
    
    [self addNSTime];
    self.pageControl.currentPage = 0;
}





//添加定时器
- (void)addNSTime{
    
    NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(nextPage) userInfo:nil repeats:YES];
    //添加到runloop中
    [[NSRunLoop mainRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
//    [timer fire];
    self.timer = timer;
}

//删除定时器
- (void)removeNSTimer{
    
    [self.timer invalidate];
    self.timer = nil;
}

//自动滚动
- (void)nextPage{
    
    NSInteger currentNumber = self.pageControl.currentPage;
    CGFloat x = ((currentNumber + 1)%3) * self.collectionView.bounds.size.width;
    
    if (currentNumber <= 5) {
        [self.collectionView setContentOffset:CGPointMake(x, 0) animated:YES];
        
    }else{
        [self.collectionView setContentOffset:CGPointMake(x, 0) animated:NO];
    }
    
    self.pageControl.currentPage = x;
    
}

```
3.实现代理方法，并进行相关操作
```
#pragma mark --)<UICollectionViewDataSource,UICollectionViewDelegate>


- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView{
    
    return 1;
}

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section{
    
    return 3;
    
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath{
    
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:cellIdef_ forIndexPath:indexPath];
    cell.backgroundColor = [UIColor yellowColor];
    
    
    UIImageView *image = [[UIImageView alloc]initWithImage:[UIImage imageNamed:[NSString stringWithFormat:@"%ld.jpg",indexPath.row + 1]]];
    image.frame = cell.bounds;
    [cell addSubview:image];
    
    return cell;
    
}


//当用户开始拖拽的时候就调用
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView{
    
    [self removeNSTimer];
}


//当用户停止拖拽的时候调用
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate{
    
    [self addNSTime];
}

//设置页码
- (void)scrollViewDidScroll:(UIScrollView *)scrollView{
    
    int page = (int)(scrollView.contentOffset.x/scrollView.bounds.size.width)%3;
    
    self.pageControl.currentPage = page;
    
}
```
